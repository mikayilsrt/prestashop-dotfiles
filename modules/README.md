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
ALTER TABLE `ps_category` DROP `color`;
```

### Hook
---

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
