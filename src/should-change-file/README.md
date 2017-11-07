# 要改的文件
1 node_modules/.0.9.16@swagger-tools/middleware/swagger-router.js
文件开头加：
```
var Mock = require('../lib/mock')
var Random = Mock.Random
```
修改 getMockValue 方法。代码如下  
```
var getMockValue = function (version, schema) {
  var type = _.isPlainObject(schema) ? schema.type : schema;
  var xmock =_.isPlainObject(schema) ? schema['x-mock'] : false;
  var value;

  if (!type) {
    type = 'object';
  }

  switch (type) {
  case 'array':
    let listLen = Random.integer(1, 9)
    value = _mockItem(listLen);
    function _mockItem(num) {
      var res = []
      for (var i = 0; i < num; i++) {
        res.push(getMockValue(version, _.isArray(schema.items) ? schema.items[0] : schema.items))
      }
      return res
    }
    break;

  case 'boolean':
    value = Random.boolean()

    break;

  case 'file':
  case 'File':
    value = 'Pretend this is some file content';

    break;

  case 'integer':
    if (version === '1.2' && !_.isUndefined(schema.defaultValue)) {
      value = schema.defaultValue;
    } else if (version === '2.0' && !_.isUndefined(schema.default)) {
      value = Random.integer();
    } else if (_.isArray(schema.enum)) {
      let randomEnumLen = Random.integer(0, schema.enum.length - 1)
      value = schema.enum[randomEnumLen];
    } else {
      value = Random.integer();
    }

    // Convert value if necessary
    if (!_.isNumber(value)) {
      value = parseInt(value, 10);
    }

    // TODO: Handle constraints and formats

    break;

  case 'object':
    value = {};

    _.each(schema.allOf, function (parentSchema) {
      _.each(parentSchema.properties, function (property, propName) {
        value[propName] = getMockValue(version, property);
      });
    });

    _.each(schema.properties, function (property, propName) {
      value[propName] = getMockValue(version, property);
    });

    break;

  case 'number':
    if (version === '1.2' && !_.isUndefined(schema.defaultValue)) {
      value = schema.defaultValue;
    } else if (version === '2.0' && !_.isUndefined(schema.default)) {
      value = Random.float();
    } else if (_.isArray(schema.enum)) {
      let randomEnumLen = Random.integer(0, schema.enum.length - 1)
      value = schema.enum[randomEnumLen];
    } else {
      value = Random.float();
    }

    // Convert value if necessary
    if (!_.isNumber(value)) {
      value = parseFloat(value);
    }

    // TODO: Handle constraints and formats

    break;

  case 'string':

    if (version === '1.2' && !_.isUndefined(schema.defaultValue)) {
      value = schema.defaultValue;
    } else if (version === '2.0' && !_.isUndefined(schema.default)) {
      value = schema.default;
    } else if (_.isArray(schema.enum)) {
      value = schema.enum[0];
    } else {
      if (schema.format === 'date') {
        value = new Date().toISOString().split('T')[0];
      } else if (schema.format === 'date-time') {
        value = new Date().toISOString();
      } else {
        value = Random.word();
      }
    }

    break;
  }
  function S4() {
    return (((1+Math.random())*0x10000)|0).toString(16).substring(1); 
  }

  if(xmock === 'id') {
    value = (S4() + S4() + "-" + S4() + "-4" + S4().substr(0,3) + "-" + S4() + "-" + S4() + S4() + S4()).toLowerCase()
  } else if(xmock === 'pager') {
    value = Random.integer(0, 60)
  }

  return value;
}
```

2 将当前文件夹下 `mock.js` 复制到 `node_modules/.0.9.16@swagger-tools/lib/` 下。

