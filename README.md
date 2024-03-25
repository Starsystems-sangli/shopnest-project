# ShopNest Project MVT Approach

**Step-by-Step Guide to Creating Your First Django Project: An Online eCommerce App**

Welcome to the world of Django! In this comprehensive guide, we'll walk you through the process of creating your first Django project—an online eCommerce app. By following these steps, you'll gain a solid understanding of Django's core concepts, including models, serializers, views, and URL routing. Let's get started!

**Note:** If you want your files to be saved, please follow the instructions given below:

- After entering the Digilabs Lab, open the `Files` app and navigate to the folder named **YOUR_REGISTER_NUMBER**, for example, **SBDEMO001**.
- Create a new folder named `Django-Practice`. Double-click to open the folder, then right-click to open a terminal.
- Whenever you open a terminal, the first command you should type is the following command, which will show you the current folder you are in:

```sh
bash
```

**Step 1: Set Up Your Development Environment**

Before diving into Django development, ensure you have Python and Django installed on your system. You can install Django using pip, the Python package manager:

```bash
pip install django
```

Install Django Rest Framework (DRF) for building Web APIs:

```bash
pip install djangorestframework
```

Ensure you have MySQL installed on your system, and then install the MySQL client for Python:

```bash
pip install mysqlclient
```

**Step 2: Create a Django Project**

Now, create a new Django project using the following command:

```bash
django-admin startproject shopnest
```

Navigate into your project directory:

```bash
cd shopnest
```

**Step 3: Create Your products App**

Next, create your products app within the Django project:

```bash
python3 manage.py startapp products
```

Next, Register Your app in `settings.py` file `INSTALLED_APPS` sections

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'products', # Register here whenever you create new app.
]
```

**Step 4: Configure MySQL as the Database Backend**

Open terminal and type mysql, since in your login dont't have password set you can directly use the mysql database

```sh
mysql -u root

create database shopnest
```

In your project's `settings.py` file, configure MySQL as the database backend by updating the `DATABASES` setting:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'shopnest',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

Replace `'root'` and `''` with your MySQL username and password, respectively. If you are using your own local laptop.

**Step 5: Start the Development Server**
Whenever you make any changes to the code automatically this restart the app to take the change in effect immediately. If you want the server to be stoped simply hit **CTRL+Z**

```sh
python3 manage.py runserver
```

**Step 6: Define Your Models**

In your eCommerce app's `models.py` file, define the models for your online store. For example, you might have models for `Product`, `Order`, `Customer`, etc.

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
```

**Step 7: Create Migrations and Apply Them**

Run the following commands to create migrations for the product model and apply them to create the corresponding database table:

```sh
python3 manage.py makemigrations products
python3 manage.py migrate

```

**Step 8: Create a Form for Product Creation and Update**

Create a Django form for product creation and update in the products/forms.py file:

```python
from django import forms
from .models import Product

class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'description', 'price']
```

**Step 9: Create Views for CRUD Operations**

In the products/views.py file, create views for CRUD operations:

```python
from django.shortcuts import render, get_object_or_404, redirect
from .models import Product
from .forms import ProductForm

def product_list(request):
    products = Product.objects.all()
    return render(request, 'products/product_list.html', {'products': products})

def product_detail(request, pk):
    product = get_object_or_404(Product, pk=pk)
    return render(request, 'products/product_detail.html', {'product': product})

def product_create(request):
    if request.method == 'POST':
        form = ProductForm(request.POST)
        if form.is_valid():
            product = form.save()
            return redirect('product_detail', pk=product.pk)
    else:
        form = ProductForm()
    return render(request, 'products/product_form.html', {'form': form})

def product_update(request, pk):
    product = get_object_or_404(Product, pk=pk)
    if request.method == 'POST':
        form = ProductForm(request.POST, instance=product)
        if form.is_valid():
            product = form.save()
            return redirect('product_detail', pk=product.pk)
    else:
        form = ProductForm(instance=product)
    return render(request, 'products/product_form.html', {'form': form})

def product_delete(request, pk):
    product = get_object_or_404(Product, pk=pk)
    if request.method == 'POST':
        product.delete()
        return redirect('product_list')
    return render(request, 'products/product_confirm_delete.html', {'product': product})
```

**Step 10: Create Templates for Views**
In the Django code snippet provided, the line `return render(request, 'products/product_form.html', {'form': form})` is used to render an HTML template named `product_form.html`. This template is expected to be located within a directory named `products` inside the `templates` directory of your Django app.

Here's the directory structure you should have:

```
shopnest-project
├── shopnest
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── products
│   ├── migrations
│   │   └── ...
│   ├── templates
│   │   └── products
│   │       └── product_form.html    <-- Your HTML template file
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── serializers.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
└── manage.py
```

You need to create a directory named `templates` inside your products directory (if it doesn't already exist), and then within the `templates` directory, create another directory named `products`. Finally, place your HTML template file, `product_form.html`, inside the `products` directory.

This directory structure ensures that Django can locate and load the `product_form.html` template when rendering the view. If you have multiple templates for different views, you can organize them similarly within the `templates` directory, creating subdirectories for each app as needed.

The content of the `product_form.html` template will depend on the functionality you want to achieve and the design of your form. However, for a basic form to create or update a product, you'll typically include HTML form elements and Django template tags to handle form rendering and submission.

Here's an example of what the `products/templates/product/product_form.html` template might look like:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Add Product</title>
	</head>
	<body>
		<h1>Add Product</h1>
		<form method="post">
			{% csrf_token %}
			<label for="name">Name:</label><br />
			<input type="text" id="name" name="name" required /><br />
			<label for="description">Description:</label><br />
			<textarea
				id="description"
				name="description"
				rows="4"
				cols="50"
			></textarea
			><br />
			<label for="price">Price:</label><br />
			<input
				type="number"
				id="price"
				name="price"
				step="0.01"
				min="0"
				required
			/><br />
			<button type="submit">Add Product</button>
		</form>
	</body>
</html>
```

Explanation of the HTML code:

- The form is submitted using the POST method.
- `{% csrf_token %}` is used to include a CSRF token for security.
- Input fields are provided for the product name, description, and price.
- Each input field has a corresponding `<label>` element for accessibility.
- The `required` attribute ensures that the fields are mandatory.
- The `<button>` element is used to submit the form.

This HTML code represents a basic form for adding a new product. You can further enhance it with styling, validation, or additional form elements as per your project requirements.

**Restart Development Server**

once you created the above template restart your development server by hitting **CTRL+Z**

```python
python3 manage.py runserver
```

This is a basic example. Depending on your requirements, you may need to customize the form layout, add validation messages, include JavaScript for client-side validation, or use form widgets for better user interaction.

next, is an example of what the `product_detail.html` template might look like:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Product Detail</title>
	</head>
	<body>
		<h1>Product Detail</h1>
		<p><strong>Name:</strong> {{ product.name }}</p>
		<p><strong>Description:</strong> {{ product.description }}</p>
		<p><strong>Price:</strong> {{ product.price }}</p>
		<!-- Add additional product details as needed -->
	</body>
</html>
```

Explanation of the template:

- The template renders details of a single product.
- `{{ product.name }}`, `{{ product.description }}`, and `{{ product.price }}` are Django template variables representing attributes of the `product` object passed from the view.
- You can expand the template to include more product details or customize the layout as per your requirements.

Make sure the `product_detail.html` template file is saved in the correct location (`products/templates/products/product_detail.html`) and that it's correctly referenced in the `product_detail` view.

next, is an example of what the `product_list.html` template might look like:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Product List</title>
	</head>
	<body>
		<h1>Product List</h1>
		<ul>
			{% for product in products %}
			<li>
				<a href="{% url 'product_detail' product.pk %}">{{ product.name }}</a>
				<!-- Optionally display other product details -->
			</li>
			{% empty %}
			<li>No products available</li>
			{% endfor %}
		</ul>
	</body>
</html>
```

Explanation of the template:

- The template renders a list of products.
- It uses a Django template `for` loop to iterate over each product in the `products` queryset.
- For each product, it displays the product name and optionally other details (such as description and price).
- The product name is rendered as a hyperlink that points to the product detail page using the `{% url %}` template tag with the `product_detail` view name and the product's primary key (`product.pk`).
- If there are no products available, it displays a message indicating so.

Make sure the `product_list.html` template file is saved in the correct location (`products/templates/products/product_list.html`) and that it's correctly referenced in the `product_list` view.

next, the `product_confirm_delete.html` template might look like:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Delete Product</title>
	</head>
	<body>
		<h1>Delete Product</h1>
		<p>Are you sure you want to delete the product "{{ product.name }}"?</p>
		<form method="post">
			{% csrf_token %}
			<button type="submit">Confirm Delete</button>
		</form>
	</body>
</html>
```

Explanation of the template:

- The template renders a confirmation message before deleting a product.
- It displays the name of the product being deleted.
- The form is submitted using the POST method.
- `{% csrf_token %}` is used to include a CSRF token for security.
- A button is included to confirm the deletion.

Make sure the `product_confirm_delete.html` template file is saved in the correct location (`products/templates/products/product_confirm_delete.html`) and that it's correctly referenced in the appropriate view.

**Step 11: Configure URLs**

In the shopnest/urls.py file, configure URLs to map views to specific endpoints:

```python
from django.urls import path
from products import views

urlpatterns = [
    path('product/', views.product_list, name='product_list'),
    path('product/<int:pk>/', views.product_detail, name='product_detail'),
    path('product/new/', views.product_create, name='product_create'),
    path('product/<int:pk>/edit/', views.product_update, name='product_update'),
    path('product/<int:pk>/delete/', views.product_delete, name='product_delete'),
]

```

## Testing the Application

**Create a New Product**

Hit the following URL in the browser to Create a new product to the database

```sh
http://127.0.0.1/product/new
```

**Get One Product By Id**

Hit the following URL in the browser to get product by id

```sh
http://127.0.0.1/product/1/
```

**Get All Products**

Hit the following URL in the browser to get all products

```sh
http://127.0.0.1/product/1/
```

**Update Product By Id**

Hit the following URL in the browser to update product by id

```sh
http://127.0.0.1/product/1/edit/
```

**Delete the Product By ID**

Hit the following URL in the browser to delete the product by id

```sh
http://127.0.0.1/product/1/delete/
```
