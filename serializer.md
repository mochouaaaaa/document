#### ModelSerializer 自定义字段验证

- validate_fields  验证时可以传递任何类型进来，避免了序列化的单一类型限制，必须抛出序列化自己异常方便处理异常类型辨别是参数验证还是自己自定义的异常

  ```python
  from rest_framework.exceptions import ValidationError
  
  
  class CreateDepartmentSerializer(ModelSerializer):
      dep_path = serializers.CharField(read_only=True)
  
      def validate_parent_id(self, parent_id):
          if parent_id is None:
              return parent_id
          elif isinstance(parent_id, Department):
              return parent_id
          raise ValidationError(detail="parent_id 参数类型有误")
  ```



- extra_kwargs  自定义参数错误提示,  统一异常定义错误的地方避免自己重复查询数据库或者写函数等多余操作, 当初抛出自定写的异常时需要将序列化验证is_valid方法raise_exception改为True

  ```python
  from rest_framework import serializers
  
  
  class ModelSerializer(serializers.ModelSerializer):
  
      def is_valid(self, raise_exception=True):
          return super(ModelSerializer, self).is_valid(raise_exception=raise_exception)
  
  
  class CreateDepartmentSerializer(ModelSerializer):
      dep_path = serializers.CharField(read_only=True)
  
      def validate_parent_id(self, parent_id):
          if parent_id is None:
              return parent_id
          elif isinstance(parent_id, Department):
              return parent_id
          raise ValidationError(detail="parent_id 参数类型有误")
  
      class Meta:
          extra_kwargs = {
              "department_name": {
                  # 这里是验证部门是否已经存在
                  "validators": [
                      validators.UniqueValidator(
                          queryset=Department.objects.all(),
                          message=f"部门已存在"
                      )
                  ],
              },
              "parent_id": {
                  # 这里是重写异常提示
                  "error_messages": {
                      "does_not_exist": "上级部门不存在"
                  }
              }
          }
          model = Department
          fields = '__all__'
  
      def create(self, validated_data):
          return Department.objects.create(**data)
  ```
  
  

