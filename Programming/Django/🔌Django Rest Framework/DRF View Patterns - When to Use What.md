## Generic Views (UpdateAPIView, etc.)

**Best for:** Standard CRUD operations with querysets

### Typical Pattern:

```python
# URL: /api/users/123/
class UserUpdateView(UpdateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

### Flow:

1. `get_queryset()` returns `User.objects.all()`
2. `get_object()` filters queryset: `queryset.get(pk=123)`
3. Updates user with ID from URL

### Use when:

- URL has object ID/slug: `/api/users/123/`, `/api/posts/my-slug/`
- Need to lookup objects from database
- Working with multiple potential objects
- Standard CRUD operations
- Need queryset filtering/permissions

## Function-Based Views or GenericAPIView + Mixins

**Best for:** User-specific operations

### Pattern:

```python
# URL: /api/change-password/ (no ID needed)
@api_view(['POST'])
@permission_classes([IsAuthenticated])
def change_password(request):
    # Always works with request.user
```

### Flow:

1. Direct access to `request.user`
2. No database lookup required
3. No queryset filtering needed

### Use when:

- Always the same object (`request.user`)
- URL has no object parameters
- User-specific operations (password change, profile update, settings)
- Security by design (users can only modify their own data)
- Simple, specific operations that don't follow standard CRUD

## Key Insight

**Generic views assume you need to "find" an object from many possible objects.**

**Function-based views are better when you already know exactly which object you're working with.**

## Common User-Specific Operations (Use FBV):

- Change password
- Update own profile
- Get own settings
- Upload own avatar
- View own dashboard

## Common Generic Operations (Use Generic Views):

- Admin updating any user
- Public API with object IDs
- Standard model CRUD
- List/filter operations