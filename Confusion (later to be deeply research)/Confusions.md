
Filtering via get_queryset or overriding get_object for complex lookups.



post localhost:8000/user -> Anyone can login
get localhost:8000/user -> The user should be logged in to see other user, or the user should be admin [ISAuthenticated(), IsAdminUSer()]

patch localhost:8000/user/1 (retrieve a specific user)
[IsAuthenticated(), IsSelfOrAdmin()]

IsSelfOrAdmin -> get the user instance -> check if the user is the one who is currently logged in ->
Or the user is the one who is admin of the site
Object level permissions -> Permission checked per object instance. For example: A user can only update their own profile, user can only add their profile picture

Similarly :
get localhost:8000/novels -> Any user can see the novels

get localhost:8000/novels/1 -> [IsAuthenticated()]. It means to see a individual novel, the user should be authenticated, anyone who is authenticated can see

post localhost:8000/novels -> ISAuthenticated()

patch /novels/1 or delete novels/1 -> Only the owner of the novel should be able to update their novel or delete their novel unless the user is admin.
It means we need to create a object level permission. 
IsOwnerOrAdmin -> checks the request.user to current novel author 
if request.user == obj.author 

Similarly for the novel/1/comments:
get novel/1/comments -> Allowany

get novel/1/comments/1 -> IsAuthenticated
post novel/1/comments -> IsAuthenticated

patch novel/1/comments/1 -> IsCommentOwner
delete novel/1/comments/1 -> IsCommentOwnerOrAuthor -> obj.user_id = request.user , obj.novel.author_id = user.id or user.is_staff



