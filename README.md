# IT314 Software Engineering

# Lab - 5

### Name : Ayush Patel
### Student ID : 202001410

## Static Analysis of code using Static analysis tools

Git Repo : https://github.com/TheAbhijeet/Django_blog.git

Static Analysis done in view.py file present in blog folder

### views.py

```python
from django.shortcuts import get_object_or_404, render
from django.views import generic
import numpy
import matplotlib

from .forms import CommentForm
from .models import Post


class PostList(generic. ListView):
    queryset = Post.objects.filter(status=1).order_by("-created_on")
    template_name = "index.html"
    paginate_by=3  


# class PostDetail(generic.DetailView):
#     model = Post
#     template_name = 'post_detail.html'


def post_detail(request, slug,a):
    template_name = "post_detail.html" 
    # post = get_object_or_404(Post, slug=slug)
    comments = post.comments.filter(active=True).order_by("-created_on")
    new_comment = None
    # Comment posted
    if request.method == "POST":
        comment_form = CommentForm(data=request.POST)
        if comment_form.is_valid():

            # Create Comment object but don't save to database yet
            new_comment = comment_form.save(commit=False)
            # Assign the current post to the comment
            new_comment.post = post
            #Save the comment to the database
            new_comment.save()
    else:
        comment_form = CommentForm( )

    return render(
        request,
        template_name,
        {
            "post": post,
            "comments": comments,
            "new_comment": new_comment,
            "comment_form": comment_form,
        },
    )
```

### Error recognised by mypy

    PS C:\Users\student\Desktop\202001410\Django_blog-master> py -m mypy .\blog\views.py  
    blog\models.py:1: error: Cannot find implementation or library stub for module named "django.contrib.auth.models"  [import]
    blog\models.py:2: error: Cannot find implementation or library stub for module named "django.db"  [import]   
    blog\models.py:25: error: Cannot find implementation or library stub for module named "django.urls"  [import]
    blog\forms.py:1: error: Cannot find implementation or library stub for module named "django"  [import]
    blog\views.py:1: error: Cannot find implementation or library stub for module named "django.shortcuts"  [import]
    blog\views.py:1: note: See https://mypy.readthedocs.io/en/stable/running_mypy.html#missing-imports
    blog\views.py:2: error: Cannot find implementation or library stub for module named "django.views"  [import]
    blog\views.py:4: error: Skipping analyzing "matplotlib": module is installed, but missing library stubs or py.typed marker  [import]
    Found 7 errors in 3 files (checked 1 source file)
    
### Error recognised by flake8

    PS C:\Users\student\Desktop\202001410\Django_blog-master> py -m flake8 .\blog\views.py
    .\blog\views.py:1:1: F401 'django.shortcuts.get_object_or_404' imported but unused
    .\blog\views.py:3:1: F401 'numpy' imported but unused
    .\blog\views.py:4:1: F401 'matplotlib' imported but unused
    .\blog\views.py:13:16: E225 missing whitespace around operator
    .\blog\views.py:13:18: W291 trailing whitespace
    .\blog\views.py:21:30: E231 missing whitespace after ','
    .\blog\views.py:22:39: W291 trailing whitespace
    .\blog\views.py:24:16: F821 undefined name 'post'
    .\blog\views.py:34:32: F821 undefined name 'post'
    .\blog\views.py:35:13: E265 block comment should start with '# '
    .\blog\views.py:38:36: E201 whitespace after '('
    .\blog\views.py:44:21: F821 undefined name 'post'
    
### Error recognised by pylint

    PS C:\Users\student\Desktop\202001410\Django_blog-master> py -m pylint .\blog\views.py
    ************* Module blog.views
    blog\views.py:13:17: C0303: Trailing whitespace (trailing-whitespace)        
    blog\views.py:22:38: C0303: Trailing whitespace (trailing-whitespace)        
    blog\views.py:1:0: C0114: Missing module docstring (missing-module-docstring)
    blog\views.py:1:0: E0401: Unable to import 'django.shortcuts' (import-error) 
    blog\views.py:2:0: E0401: Unable to import 'django.views' (import-error)     
    blog\views.py:10:0: C0115: Missing class docstring (missing-class-docstring)
    blog\views.py:10:0: R0903: Too few public methods (0/2) (too-few-public-methods)
    blog\views.py:21:0: C0116: Missing function or method docstring (missing-function-docstring)
    blog\views.py:21:30: C0103: Argument name "a" doesn't conform to snake_case naming style (invalid-name)
    blog\views.py:24:15: E0602: Undefined variable 'post' (undefined-variable)
    blog\views.py:34:31: E0602: Undefined variable 'post' (undefined-variable)
    blog\views.py:44:20: E0602: Undefined variable 'post' (undefined-variable)
    blog\views.py:21:25: W0613: Unused argument 'slug' (unused-argument)
    blog\views.py:21:30: W0613: Unused argument 'a' (unused-argument)
    blog\views.py:1:0: W0611: Unused get_object_or_404 imported from django.shortcuts (unused-import)
    blog\views.py:3:0: W0611: Unused import numpy (unused-import)
    blog\views.py:4:0: W0611: Unused import matplotlib (unused-import)

    ------------------------------------------------------------------
    Your code has been rated at 0.00/10 (previous run: 0.00/10, +0.00)
    
## Observation among tools used mypy, flake8, pylint

- In all 3 tools, once it encounters syntax error then it will not show any other errors.
- In mypy, it doesnt show any error if the imported module is not used while in flake8 and pylint, it shows error for that.
- Undefined variable error is not recognised by mypy while it is recognised by flake8 abd pylint.
- Only flake8 show error for block comment should start with '#'.
- From analysis we can say that flake8 and pylint are more efficient tool than mypy.
