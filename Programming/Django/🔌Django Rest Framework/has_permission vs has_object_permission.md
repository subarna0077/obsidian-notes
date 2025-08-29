# Permission Methods: has_permission vs has_object_permission

## Quick Overview

- **has_permission**: View-level access control _(before object fetch)_
- **has_object_permission**: Object-level access control _(after object fetch)_

---

## has_permission

### 🎯 **Purpose**

Controls access to the **entire endpoint/view** before processing

### ⏱️ **When Called**

- First in the permission chain
- Before any database queries
- Before object retrieval

### 🔧 **Use Cases**

- Authentication checks (`user.is_authenticated`)
- Role/group membership (`user.is_staff`)
- Endpoint-level restrictions
- Rate limiting
- Subscription/plan-based access

### 💡 **Example**

```python
def has_permission(self, request, view):
    # Only staff can create posts
    if view.action == 'create':
        return request.user.is_staff
    return request.user.is_authenticated
```

---

## has_object_permission

### 🎯 **Purpose**

Controls access to **specific objects/instances**

### ⏱️ **When Called**

- After `has_permission` passes
- After object is fetched from database
- Only for detail views (retrieve, update, delete)

### 🔧 **Use Cases**

- Ownership checks (`obj.owner == user`)
- Relationship-based permissions
- Object status/state checks
- Complex business logic with object data

### 💡 **Example**

```python
def has_object_permission(self, request, view, obj):
    # Users can only edit their own posts
    return obj.author == request.user
```

---

## Key Differences

| Aspect           | has_permission             | has_object_permission          |
| ---------------- | -------------------------- | ------------------------------ |
| **Timing**       | Before object fetch        | After object fetch             |
| **Scope**        | Endpoint/View level        | Individual object              |
| **Performance**  | Lightweight, no DB queries | Heavier, object already loaded |
| **Required for** | All actions                | Detail actions only            |

---

## Flow Diagram

```
Request → has_permission() → [PASS] → Fetch Object → has_object_permission() → [PASS] → Process Request
                          ↓ [FAIL]                                        ↓ [FAIL]
                       403 Forbidden                                   403 Forbidden
```

---

## Common Patterns

### 🔄 **Layered Security**

```python
class MyPermission(BasePermission):
    def has_permission(self, request, view):
        # Gate 1: Must be authenticated
        return request.user.is_authenticated
    
    def has_object_permission(self, request, view, obj):
        # Gate 2: Must own object or be admin
        return obj.owner == request.user or request.user.is_admin
```

### ⚡ **Performance Tip**

Always put lightweight checks in `has_permission` to avoid unnecessary database queries.

---

## Quick Decision Guide

**Use has_permission when:**

- ✅ Check doesn't need specific object data
- ✅ Want to block access to entire endpoint
- ✅ Checking user roles/authentication
- ✅ Want better performance (no DB queries yet)

**Use has_object_permission when:**

- ✅ Need to check object attributes
- ✅ Ownership or relationship validation
- ✅ Business logic depends on object state
- ✅ Working with detail views (get/put/patch/delete)

---


DRF provides **two types of permissions**:

1. **Base-level (general) permissions**
    
    - Checked **before accessing any object**.
        
    - Controls **who can perform an action** (create, list, etc.) at the **view/action level**.
        
2. **Object-level permissions**
    
    - Checked **per object** after the object is fetched from the database.
        
    - Controls **who can access or modify a specific object instance**.
        

---

## **2. Base-level Permissions**

- Implemented via:
    

`permission_classes = [...] # or dynamically using get_permissions()`

- **Checked for:**
    
    - `list`, `create` (because there is no specific object yet).
        
- **Example:**
    

`def get_permissions(self):     if self.action == 'list':         return [AllowAny()]            # Anyone can view     if self.action == 'create':         return [IsAuthenticated()]     # Any logged-in user can create     return [IsCommentOwnerOrAuthorOrReadOnly()]  # object-level for other actions`

- ✅ **Key point:** Base-level permission decides **if a user can perform an action at all**.
    

---

## **3. Object-level Permissions**

- Implemented via a **custom class** with `has_object_permission`:
    

`from rest_framework.permissions import BasePermission, SAFE_METHODS  class IsCommentOwnerOrAuthorOrReadOnly(BasePermission):     def has_object_permission(self, request, view, obj):         # Read-only access for everyone         if request.method in SAFE_METHODS:             return True                  user = request.user         # Must be logged in for write operations         if not user or not user.is_authenticated:             return False                  # Write permissions for:         # 1. Comment owner         # 2. Novel author         # 3. Admin/staff         return (             obj.user_id == user.id or             obj.novel.author_id == user.id or             user.is_staff         )`

- **Checked for:**
    
    - `retrieve`, `update`, `partial_update`, `destroy` (actions with a specific object).
        
- ✅ **Key point:** Object-level permission ensures **only the owner, related author, or admin can modify the object**.
    

---

## **4. How DRF processes permissions in a ViewSet**

1. **Authenticate the user** (e.g., `JWTAuthentication`)
    
2. **Check base-level permissions** (via `permission_classes` / `get_permissions`)
    
3. **If action is object-specific** (`retrieve`, `update`, `destroy`):
    
    - Fetch the object
        
    - Call `has_object_permission` for that object
        
4. **Serializer is applied** for input/output
    

---

## **5. Key Takeaways**

- **Base-level**:
    
    - Checked first, before object exists.
        
    - Controls **general access to actions**.
        
    - Example: `IsAuthenticated`, `AllowAny`, `IsAdminUser`.
        
- **Object-level**:
    
    - Checked after fetching an object.
        
    - Controls **access to a specific object**.
        
    - Example: `IsCommentOwnerOrAuthorOrReadOnly`.
        
- **POST requests (create)**: Always use **base-level permission** because object doesn’t exist yet.
    
- **GET requests (list)**: Can allow **anyone** or restrict via **base-level**.
    

---

## **6. Example Summary Table**

|HTTP Method|Permission Type|Who Can Access|
|---|---|---|
|GET /list|Base-level|Anyone (`AllowAny`)|
|POST /create|Base-level|Any logged-in user (`IsAuthenticated`)|
|GET /<id>|Object-level|Owner, novel author, admin (or read-only for everyone)|
|PATCH/DELETE /<id>|Object-level|Owner, novel author, admin only|

---

This note can be stored under your Obsidian folder **`POSTGRES FULL NOTES > DRF Permissions`** (or wherever you keep backend notes).

---

If you want, I can also make a **small diagram** for this note showing **the flow: authenticate → base-level → object-level → serializer**. It makes it very easy to visualize.

Do you want me to make the diagram as well?

  

ChatGPT can make mistakes. Chec


## Tags

#permissions #django #rest-api #security #backend