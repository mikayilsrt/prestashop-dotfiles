# Module

## How to add new fields in your back office

To add a new field in your back office with Symfony you should create a new module.

### SQL
---

In your module you should add a new column in ps_category table.

To add a column
```sql
ALTER TABLE `ps_category` ADD `field_name` VARCHAR(255);
```

To remove a column
```sql
ALTER TABLE `ps_category` DROP `field_name`;
```

### Hook
---

List of all PrestaShop Hooks [here](https://devdocs.prestashop.com/1.7/modules/concepts/hooks/list-of-hooks/)

This **example** add a new text input named field_name in Category form.

```php
public function hookActionCategoryformBuilderModifier(array $params)
{
    $formBuilder = $params['form_builder'];

    $formBuilder->add('field_name', TextType::class, [
        'label' => $this->l('Field label'),
        'required' => false
    ]);

    $formBuilder->setData($params['data']);
}
```

- **$formBuilder** is a instance of \Symfony\Component\Form\FormBuilder
- **$formBuilder->add($name, $type = null, $options = [])** add a new field in category form.
- **$formBuilder->setData($params['data'])** This function update the category form datas.

This function is called in action hook to save the new input value in ps_category table.

```php
private function updateData(array $data)
{
    $category = new Category($data['id']);
    $category->field_name = $data['form_data']['field_name'];
    $category->save();
}
```

### Override classes

After added your custom column in DB table you should override the specific classes.

To override a class you should create **override/classes** folders and **Category.php** file.

- 📂 folders your_module
    - 📂 override
        - 📂 classes
            - 📄 Category.php

```php
class Category extends CategoryCore
{
    public $field_name;

    public function __construct($idCategory = null, $idLang = null, $idShop = null)
    {
        self::$definition['fields']['field_name'] = [
            'type' => self::TYPE_STRING,
            'validation' => 'isGenericName',
            'size' => 255
        ];

        parent::__construct($idCategory, $idLang, $idShop);
    }
}
```
- **self::$definition['fields']['field_name']** add a new array named **field_name** CategoryCore::$definition array.