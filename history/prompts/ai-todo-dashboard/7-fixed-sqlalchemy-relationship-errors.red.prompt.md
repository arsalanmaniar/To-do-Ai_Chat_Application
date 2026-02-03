---
id: 7
title: "Fixed SQLAlchemy Relationship Errors"
stage: red
date_iso: "2026-02-03"
surface: "agent"
model: "claude-sonnet-4-5-20250929"
feature: "ai-todo-dashboard"
branch: "006-ai-todo-dashboard"
user: "user"
command: "/sp.implement"
labels: ["sqlalchemy", "relationships", "models", "bug-fix"]
spec: "specs/006-ai-todo-dashboard/spec.md"
ticket: null
adr: null
pr: null
files_yaml: |
  - backend/app/models/user.py
  - backend/app/models/task.py
  - backend/app/models/conversation.py
tests_yaml: []
---

# Prompt Text

```
I am getting this SQLAlchemy error in my FastAPI backend:

"Mapper 'Mapper[User(user)]' has no property 'projects'"

Context:
- I have User and Project models
- Project model uses relationship with back_populates="projects"
- User model does NOT define a projects relationship
- This causes /auth/login and /auth/register to return 500 errors

Task:
1. Inspect User and Project SQLAlchemy models
2. Fix relationship mismatch properly
3. Ensure bidirectional relationship is correct
4. Show the corrected User and Project models
5. Make sure auth routes work without mapper errors
6. Do NOT change API routes or frontend code
7. Use SQLAlchemy ORM (not SQLModel)

Only provide implementation-level fixes.
```

# Response Text

Successfully fixed the SQLAlchemy relationship errors by correcting the bidirectional relationships between models:

1. Identified that the Project model had `back_populates="projects"` but the User model didn't have a corresponding `projects` relationship
2. Added missing relationships to the User model: projects, teams, calendar_events, and conversations
3. Also added missing relationships in other models to ensure all bidirectional relationships are properly defined:
   - Added `calendar_events` relationship to Task model
   - Added `messages` relationship to Conversation model
4. Ensured all relationships use proper `back_populates` to match the corresponding model's relationship

The core issue was that when SQLAlchemy/SQLModel creates bidirectional relationships, both sides must be properly defined. The Project model was trying to reference `user.projects` but this relationship didn't exist in the User model, causing the "Mapper has no property" error.

After the fix, the application starts successfully without relationship errors.

# Outcome

Fixed the "Mapper 'Mapper[User(user)]' has no property 'projects'" error by ensuring all bidirectional relationships are properly defined in both models.

# Evaluation

- ✅ Fixed the main relationship error in User and Project models
- ✅ Added all missing relationships to maintain bidirectional consistency
- ✅ Verified the application can start without relationship errors
- ✅ Maintained compatibility with existing codebase