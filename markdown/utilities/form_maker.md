# Form Maker

Our Form Maker package is designed to give developers to freedom to build forms via classes and set the fields in those classes comfortably. You can easily pass a form to a view and it will render the whole form, reducing your need to type out divs, labels, inputs and more.

[Source Code](https://github.com/grafiteinc/formMaker)

!!! warning "Form Maker by default is tightly set to Bootstrap's classes."

The form maker

## Artisan Commands

Generate a form for a specific model using this make command. It will add the ModelForm to a Forms directory in the `app/Html/Forms` namespace.
```
make:model-form {model}
```

Generate a generic form with a specific name using this command. It will add the BaseForm to a Forms directory in the `app/Html/Forms` namespace.
```
make:base-form {name}
```

## Set Alternate Connections

```
app(UserForm::class)->setConnection('alternate');
```

Or in the `UserForm` itself:

```
$connection = 'alternate';
```

## Helpers

```
form() // access the `Form` class
```

## Fields

Fields are PHP objects which define types, options, attributes etc for the Form Object. This lets you design forms and update them easily via a single object rather than having to update HTML files for multiple roles in your application.

```
FIELD_OPTIONS
type: A type string such as text or file
options: Options for <select> type
legend: A label for the legend of horizontal checkboxes
label: A label
model: Model class for the HasOne and HasMany Fields
model_options: Model options
    label: The label attribute on the model
    value: The value attribute on the model
    method: A custom method to run on the model
    params: Parameters to send to the custom method
before: Text or HTML you wish to put before an input
after: Text or HTML you wish to put after the input
view: A view path to a custom template
attributes: HTML attributes for your input field
format: Format for DateTime objects
```

There are a large collection of Fields available out of the box and a `make:field {name}` command in case you need custom ones. Fields generate a config array which is then processed by the `FieldMaker` and `FieldBuilder` to create forms for easy use.

#### Available Fields

```
Checkbox
CheckboxInline
Color
CustomFile
Date
DatetimeLocal
Decimal
Email
File
HasMany
HasOne
Hidden
Image
Month
Number
Password
Radio
RadioInline
Range
Telephone
Text
TextArea
Time
Url
Week
```

## Sections

The various Form Objects allow you to set Sections. For example, you may have a `BlogForm` and you may want one row to have three columns while the next row has two, this can be achieved with the `setSections` method.

```
$columns = 1;

public function fields()
{
    return [
        Text::make('title', [
            'required' => true,
        ]),
        Text::make('url', [
            'required' => true
        ]),
        TextArea::make('entry', []),
        Date::make('published_at', []),
    ];
}
```

By default this will build a form with single column content. If you wish to set these fields to specific layouts you need to set the `columns` to `sections`

```
$columns = 'sections';

public function fields()
{
    return [
        Text::make('title', [
            'required' => true,
        ]),
        Text::make('url', [
            'required' => true
        ]),
        TextArea::make('entry', []),
        Date::make('published_at', []),
    ];
}

public function setSections()
{
    return [
        [
            'title',
            'url',
        ],
        [
            'entry'
        ],
        [
            'published_at'
        ]
    ];
}
```

The above will produce a form that is two columns, one, and one. You can add a `key` to the sections to add a horizontal line and a header.

```
public function setSections()
{
    return [
        [
            'title',
            'url',
        ],
        'Content' => [
            'entry'
        ],
        [
            'published_at'
        ]
    ];
}
```

## Form

The Form class lets us generate simple forms with minimal code.

```
form()->action('method', 'route', 'button text', $html_attributes);
```

Generates a form using the method and route with a button, for easier addition of delete buttons and more,

```
form()->confirm('Are you sure?')->action(...);
```
Adds a confirmation popup to the button.

If you wish to handle the confirm using a modal or other JS integration you can pass a `method` name into the confirm method which will trigger that JS method:

```
form()->confirm('Are you sure you want to delete this?', 'confirmation')->action(...);
```

This will add the following to the submit button in the form:

```
onclick="confirmation(event, 'Are you sure you want to delete this?')"
```

```
form()->open($options);
```

Opens a form allowing you to specify options: action, method, attributes etc.

```
form()->model($model, $options);
```

Open a form based on a model

---

## ModelForm

Using the `make:form {model}` command you can quickly generate forms for Models. This will let you generate forms based on the model.

```
app(UserForm::class)->create();
app(UserForm::class)->edit($user);
app(UserForm::class)->delete($user);
```

Within this `UserForm` class you can set the fields in in the `fields` method:

```
public function fields()
{
    return [
        Text::make('name', [
            'required' => true,
        ]),
        Email::make('email', [
            'required' => true
        ]),
    ];
}
```

This will generate a form with these fields only. You can also set the `orientation` if you wish to use labels on the left side instead of above and `columns` if you wish to generate a form in which the fields are split into more columns.

### Custom View for Fields

You can create a custom view that the FormMaker will use for your fields. Just have you view file follow this pattern:

```
<div class="row">
    <div class="form-group">
        <label class="control-label" for="{!! $labelFor !!}">{!! $label !!}</label>
        <div class="row">
            {!! $field !!}
        </div>
    </div>
    {!! $errors !!}
</div>
```

### Relationships

FormMaker can handle the following relationships: `hasOne`,`hasMany`

You can set the Field for something like 'Roles' as below.

```
HasOne::make('role', [
    'model' => Role::class,
    'model_options' => [
        'label' => 'name',
        'value' => 'id',
        'method' => 'all',
        'params' => null,
    ]
]);
```
