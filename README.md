<div align="center">

## CWizardEngine


</div>

### Description

Ever wanted to implement your own wizard but thought that the logic to manage all the different panels would be too complex? Here's the code you've been looking for! This class implements a wizard engine. You can use it to manage all the displaying of any number of panels for your own wizard. All you have to do is create the individual wizard panels. Assign the panels to the WizardEngine along with the Previous, Next, Cancel and Finish buttons and you're done. The WizardEngine handles the rest!
 
### More Info
 
Create a new class module and paste the text into it. Name the class cWizardEngine.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Matthew Janofsky](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/matthew-janofsky.md)
**Level**          |Intermediate
**User Rating**    |4.8 (38 globes from 8 users)
**Compatibility**  |VB 5\.0, VB 6\.0
**Category**       |[Miscellaneous](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/miscellaneous__1-1.md)
**World**          |[Visual Basic](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/visual-basic.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/matthew-janofsky-cwizardengine__1-8544/archive/master.zip)





### Source Code

```
Option Explicit
Option Compare Text
'-- This class encapsulates the logic for implementing a wizard.
' Copyright Matthew Janofsky 2000
'
' To use the Wizard Engine:
' Assign the panels in the order you want them displayed.
' Panels can be any control that exposes the Visible
'  property and the Move method.
' Assign the Next, Previous, Cancel and Finish buttons.
' Make sure the first panel is in the position that
'  the panels should be displayed.
'
' Example:
' Option Explicit
'
' Private WithEvents m_oWiz As cWizardEngine
'
' Private Sub Form_Load()
'
' Set m_oWiz = New cWizardEngine
'
' '-- Add the panels in the order we want them displayed.
' m_oWiz.AddPanel Me.Frame1
' m_oWiz.AddPanel Me.Frame2
' m_oWiz.AddPanel Me.Frame3
'
' '-- Add the buttons.
' Set m_oWiz.CancelButton = Me.cmdCancel
' Set m_oWiz.FinishButton = Me.cmdFinish
' Set m_oWiz.NextButton = Me.cmdNext
' Set m_oWiz.PrevButton = Me.cmdPrev
'
' '-- Only allow the finish button on the last panel.
' m_oWiz.FinishEnabledOnAllPanels = False
'
' '-- Start the wizard.
' m_oWiz.StartWizard
'
' End Sub
'
' Use the cWizardEngine_BeforeNext() event to validate
' the user's entry on the current panel when the
' Next button is clicked.
'
' Use the cWizardEngine_AfterNext() event to do any
' pre-display logic when a new panel is displayed
' after the Next button is clicked.
'----------------------
' Property variables
'----------------------
Private m_lCurrentPanelNbr As Long
Private m_bFinishEnabledOnAllPanels As Boolean
Private m_aPanels() As Control 'Array of panels.
Private WithEvents m_cmdCancelButton As CommandButton
Private WithEvents m_cmdFinishButton As CommandButton
Private WithEvents m_cmdNextButton As CommandButton
Private WithEvents m_cmdPrevButton As CommandButton
'----------------------
' Class variables
'----------------------
Private m_lPanelCount As Long
'----------------------
' Raised Events
'----------------------
Public Event AfterNext(NewPanelNbr As Long)
Public Event BeforeNext(CurrentPanelNbr As Long, _
   Cancel As Boolean)
'----------------------
' Methods
'----------------------
Public Sub AddPanel(PanelToAdd As Control)
 On Error GoTo AddPanel_Error
 '-- Add a panel to the list.
 m_lPanelCount = m_lPanelCount + 1
 ReDim Preserve m_aPanels(m_lPanelCount)
 Set m_aPanels(m_lPanelCount) = PanelToAdd
 '-- If this wasn't the first panel, adjust the panel _
 ' dimensions and position to match the first panel.
 If m_lPanelCount > 1 Then
 With m_aPanels(1)
  m_aPanels(m_lPanelCount).Move .Left, .Top, _
      .Width, .Height
 End With
 End If
 '-- Exit the procedure.
 GoTo AddPanel_Exit
AddPanel_Error:
 Select Case Err
 '-- Add specific error cases here
 'Case ...
 Case Else:
  Err.Raise Err.Number, "cWizardEngine::AddPanel()", _
  Err.Description, Err.HelpFile, Err.HelpContext
 End Select
 Resume AddPanel_Exit
 Resume 'For debugging purposes
AddPanel_Exit:
End Sub
Public Sub StartWizard()
 On Error GoTo StartWizard_Error
 Dim X As Long
 '-- Set the command button properties.
 m_cmdCancelButton.Enabled = True
 If m_bFinishEnabledOnAllPanels = True Then
 m_cmdFinishButton.Enabled = True
 Else
 m_cmdFinishButton.Enabled = False
 End If
 m_cmdNextButton.Enabled = True
 m_cmdPrevButton.Enabled = False
 '-- Set the panel properties. Display the first panel.
 m_aPanels(1).Visible = True
 For X = 2 To m_lPanelCount
 m_aPanels(X).Visible = False
 Next
 '-- Set the current panel.
 m_lCurrentPanelNbr = 1
 '-- Exit the procedure.
 GoTo StartWizard_Exit
StartWizard_Error:
 Select Case Err
 '-- Add specific error cases here
 'Case ...
 Case Else:
  Err.Raise Err.Number, _
   "cWizardEngine::StartWizard()", _
   Err.Description, Err.HelpFile, _
   Err.HelpContext
 End Select
 Resume StartWizard_Exit
 Resume 'For debugging purposes
StartWizard_Exit:
End Sub
'-----------------------
' Properties
'-----------------------
Public Property Set CancelButton(RHS As CommandButton)
 Set m_cmdCancelButton = RHS
End Property
Public Property Get CurrentPanelNbr() As Long
 '-- Return the current panel number.
 CurrentPanelNbr = m_lCurrentPanelNbr
End Property
Public Property Set FinishButton(RHS As CommandButton)
 Set m_cmdFinishButton = RHS
End Property
Public Property Get FinishEnabledOnAllPanels() As Boolean
 FinishEnabledOnAllPanels = m_bFinishEnabledOnAllPanels
End Property
Public Property Let FinishEnabledOnAllPanels(RHS As Boolean)
 m_bFinishEnabledOnAllPanels = RHS
End Property
Public Property Set NextButton(RHS As CommandButton)
 Set m_cmdNextButton = RHS
End Property
Public Property Set PrevButton(RHS As CommandButton)
 Set m_cmdPrevButton = RHS
End Property
'-------------------------
' Class Methods
'-------------------------
Private Sub Class_Initialize()
 On Error Resume Next
 m_bFinishEnabledOnAllPanels = False
 m_lPanelCount = 0
 m_lCurrentPanelNbr = 0
End Sub
Private Sub Class_Terminate()
 On Error Resume Next
 Dim X As Long
 Set m_cmdCancelButton = Nothing
 Set m_cmdFinishButton = Nothing
 Set m_cmdNextButton = Nothing
 Set m_cmdPrevButton = Nothing
 For X = 1 To m_lPanelCount
 Set m_aPanels(X) = Nothing
 Next
End Sub
'-------------------------
' Event handlers
'-------------------------
Private Sub m_cmdCancelButton_Click()
 '-- Do nothing. It is up to the caller to handle it.
End Sub
Private Sub m_cmdFinishButton_Click()
 '-- Do nothing. It is up to the caller to handle it.
End Sub
Private Sub m_cmdNextButton_Click()
 '-- Display the next panel.
 On Error GoTo m_cmdNextButton_Click_Error
 Dim bCancel As Boolean
 '-- Give the caller a chance to cancel this event.
 RaiseEvent BeforeNext(m_lCurrentPanelNbr, bCancel)
 If bCancel = True Then
 GoTo m_cmdNextButton_Click_Exit
 End If
 m_aPanels(m_lCurrentPanelNbr + 1).Visible = True
 '-- Hide the current panel.
 m_aPanels(m_lCurrentPanelNbr).Visible = False
 '-- Increment the current panel.
 m_lCurrentPanelNbr = m_lCurrentPanelNbr + 1
 '-- Enable the Prev button.
 m_cmdPrevButton.Enabled = True
 '-- If we are now on the last panel, enable the finish
 ' button if it is not already enabled and disable
 ' the Next button.
 If m_lCurrentPanelNbr = m_lPanelCount Then
 m_cmdFinishButton.Enabled = True
 m_cmdNextButton.Enabled = False
 End If
 '-- Let the caller know we are finished.
 RaiseEvent AfterNext(m_lCurrentPanelNbr)
 '-- Exit the procedure.
 GoTo m_cmdNextButton_Click_Exit
m_cmdNextButton_Click_Error:
 Select Case Err
 '-- Add specific error cases here
 'Case ...
 Case Else:
  Err.Raise Err.Number, _
   "cWizardEngine::m_cmdNextButton_Click()", _
   Err.Description, Err.HelpFile, _
   Err.HelpContext
 End Select
 Resume m_cmdNextButton_Click_Exit
 Resume 'For debugging purposes
m_cmdNextButton_Click_Exit:
End Sub
Private Sub m_cmdPrevButton_Click()
 '-- Display the previous panel.
 On Error GoTo m_cmdPrevButton_Click_Error
 m_aPanels(m_lCurrentPanelNbr - 1).Visible = True
 '-- Hide the current panel.
 m_aPanels(m_lCurrentPanelNbr).Visible = False
 '-- Decrement the current Panel.
 m_lCurrentPanelNbr = m_lCurrentPanelNbr - 1
 '-- Enable the Next Button.
 m_cmdNextButton.Enabled = True
 '-- We are not on the last panel, so disable the
 ' Finish button.
 If m_bFinishEnabledOnAllPanels = False Then
 m_cmdFinishButton.Enabled = False
 End If
 '-- If we are on the first panel, disable the Prev button.
 If m_lCurrentPanelNbr = 1 Then
 m_cmdPrevButton.Enabled = False
 End If
 '-- Exit the procedure.
 GoTo m_cmdPrevButton_Click_Exit
m_cmdPrevButton_Click_Error:
 Select Case Err
 '-- Add specific error cases here
 'Case ...
 Case Else:
  Err.Raise Err.Number, _
   "cWizardEngine::m_cmdPrevButton_Click()", _
   Err.Description, Err.HelpFile, _
   Err.HelpContext
 End Select
 Resume m_cmdPrevButton_Click_Exit
 Resume 'For debugging purposes
m_cmdPrevButton_Click_Exit:
End Sub
```

