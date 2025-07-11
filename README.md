# EmployeeManagementDashboard-Overview
Documentation and overview of the Employee Management Power Platform solution
Employee Management Portal – Overview
A comprehensive HR portal consisting of 4 Canvas Apps and Power Automate flows designed to streamline HR processes including:

1. Onboarding
2. Offboarding
3. Reporting Structure Changes
4. Title/Role Changes

Delivered via a unified Dashboard for HR visibility and task tracking.

Dashboard:
<img width="1443" height="809" alt="image" src="https://github.com/user-attachments/assets/a83851bf-ce50-4604-8e3c-dd3f4cd4b009" />
OffBoarding Screen:
<img width="1435" height="833" alt="image" src="https://github.com/user-attachments/assets/f7b36248-fc71-437b-b64a-b6116025f4da" />
Workflow tracking: 
<img width="1302" height="731" alt="image" src="https://github.com/user-attachments/assets/bf40016a-0d24-4e8b-9d8d-8f2803727b39" />

Adding new users:
<img width="1283" height="717" alt="image" src="https://github.com/user-attachments/assets/305b0b31-87f0-40ad-aad9-2b16c98bb17d" />

Data sources:
create 3 lists in SP
1. Offboarding users
2. Offboarding tasktemplates
3. Offboardingtasks
     <img width="1825" height="579" alt="image" src="https://github.com/user-attachments/assets/0cf5caf0-9375-4f93-acf7-4c3c0134aa04" />
     <img width="1814" height="618" alt="image" src="https://github.com/user-attachments/assets/b4e49efd-409f-4f19-af1c-c002d610800a" />
     <img width="1815" height="482" alt="image" src="https://github.com/user-attachments/assets/de37e018-999b-4b5a-a44b-340a7aaefdbe" />

// Highlight overdue tasks
If(DateValue(ThisItem.EndDate) < Today() && ThisItem.TaskStatus <> "Completed", RGBA(255, 200, 200, 1), White)
// Color code new tasks
If(ThisItem.TaskStatus = "NotStarted", RGBA(255, 230, 230, 1), RGBA(230, 255, 230, 1))
----------------------------------------------------------
// Update progress on a button
CountRows(
    Filter(OffboardingTasks, RequestID.Id = ThisItem.ID && Status.Value = "Completed")
) & " of " &
CountRows(
    Filter(OffboardingTasks, RequestID.Id = ThisItem.ID)
) & " tasks completed  — "  &
Round(
    100 * CountRows(
        Filter(OffboardingTasks, RequestID.Id = ThisItem.ID && Status.Value = "Completed")
    ) /
    Max(
        1,
        CountRows(
            Filter(OffboardingTasks, RequestID.Id = ThisItem.ID)
        )
    ),
    0
) & "%"
------------------------------------------------------
// patch code for multiline text notes
Patch(
    OffboardingTasks,
    LookUp(OffboardingTasks, ID = var_EditingNoteID),
    {
        Notes: var_NotesValue
    }
);
Set(var_SavedNoteID, var_EditingNoteID);
Set(var_EditingNoteID, Blank());
Set(var_NotesValue, Blank());
Set(var_StartTimer, true)
------------------------------------------------------------
// User/ manager filed combo box: Office365Users.SearchUser({searchTerm: Self.SearchText})

---------------------------------------------------------
-----------------------------------------------------------
                    Workflow

---------------------------------------------------------
-----------------------------------------------------------
Tasks will be assigned based on Category - any one of the assignee user can approve a task - WF for single user formula is added
1. Setting offset days: based on each task for a user/ req ID
2. Assigned to field is multiuser selection and user  and groups supported

-------------------------------
** Power Automate Flow That Supports Multiple AssignedTo Users and calculating offset days for each task - 	Flow should avoid infinite loops or broken due date calculations 
stable flow that does the following:
•	Trigger	When a new item is created in NewHires
•	Get Task Templates	Pull all TaskTemplates
•	Loop over each task	For each row in TaskTemplates
•	Loop over AssignedTo users	For each user in that template’s AssignedTo people field
•	Create separate task	Add 1 task per person to OnboardingTasks
•	DueDate logic	Set using StartDate + OffsetDays

<img width="998" height="566" alt="image" src="https://github.com/user-attachments/assets/85a8bb5e-d0f7-4a0e-a6bb-60ac7472c4a0" />
<img width="1045" height="653" alt="image" src="https://github.com/user-attachments/assets/9a86afd4-75da-4080-bef9-d0635936ce77" />
<img width="956" height="713" alt="image" src="https://github.com/user-attachments/assets/1ec1d1d9-701d-4dc4-a98f-37d98056c927" />
claims: concat('i:0#.f|membership|', toLower(item()?['Email']))
<img width="880" height="661" alt="image" src="https://github.com/user-attachments/assets/8b44b561-37e6-4f5e-a6fd-18b1c97104d1" />

AddDays: addDays(variables('var_StartDate'), int(items('LoopTaskTemplate')?['OffsetDays']))


*** Avoid indefiniteloop by changing assigned to filed
<img width="1024" height="267" alt="image" src="https://github.com/user-attachments/assets/f000913e-e1e8-4693-bea4-814f17b31c24" />




   

