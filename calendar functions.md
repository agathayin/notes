# notes

### Calendar([React Scheduler](https://devexpress.github.io/devextreme-reactive/react/scheduler/docs/guides/getting-started/))
#### Structure
> [Calendar]
> + Scheduler
>> + ViewState
>> + EditingState
>> + WeekView
>> + MonthView
>> + EditRecurrenceMenu (editing and deleting)
>> + Appointments [AppointmentContentContainer]
>> + AppointmentTooltip [AppointmentTooltipContainer]+[AppointmentTooltipHeader]->[DeleteModal]&[EditPromotionModal]
>> + Toolbar
>> + ViewSwitcher (switch week and month)
>> + DateNavigator
>> + AppointmentForm [AppointmentFormContainerBasic]
>> + DragDropProvider
>> + CurrentTimeIndicator
> + Modal (Delete comfirmation)
> + Modal (Select Course)
>> + @EtomonSearchClientAutocomplete 

> [ComponentStyles](only styles for custom components)

#### Diagram
##### getting data
[calendar]componentDidMount => currentDateChange => transferData(grants)

##### adding grants
**[EtomonSearchClientAutocomplete]**: getSearchResult & confirmSelectedCourse (result => chosenCourse => addedAppointmenet) =>
**[AppointmentFormContainerBasic]**: applyChanges => commitAppointment("added") => this.saveChanges(this.transferFrequency(appointment), "added") => await lessonTool.api.addBuyLessonGrant(null, {...}); => this.props.commitChanges
`response = await lessonTool.api.addBuyLessonGrant(null, {
              allowPurchasesEvery: data.cronString,
              endTime: moment.utc(data.endDate).toISOString(),
              startTime: moment.utc(data.startDate).toISOString(),
              validBefore: new Date(moment.utc(data.endDate)+data.sessionDuration).toISOString(),
              validAfter: new Date().toISOString(),
            });`
            
##### changing grants
**[AppointmentFormContainerBasic]**: applyChanges => commitAppointment("changed") => `this.saveChanges(this.transferFrequency(appointment), "changed")` => generateGrant => `await lessonTool.api.livePatchBuyLessonGrant(data.id, changeset);` => this.props.commitChanges

##### deleting grants
###### delete on form
**[AppointmentFormContainerBasic]**: this.commitAppointment("deleted") => commitChanges({ [type]: appointment.id }); 
###### delete on tooltip header
**[AppointmentTooltipHeader]**: openDeleteModal =>
**[DeleteModal]**: commitDeletedAppointment/commitDeleteAll =>
**[AppointmentTooltipHeader]**: deleteAppointment(this.state.deleteOption) =>
**[calendar]**: deleteAppointmentOption(option, this.props.appointmentData) => onDeleteAppointmentOption(type,currentData) => this.commitDeleteCurrentClass(id, date)/this.commitDeleteAllInDay(id,date)/this.commitDeleteAllAppointments(id) => (this is only for deleting)`await tool.api.removeBuyLessonGrant(this.state.deletedAppointmentId)` => setState({ data: nextData, deletedAppointmentId: null })

##### error
**[AppointmentFormContainerBasic]**: sendErrorMsg(error) =>
**[calendar]**: updateErrMsg
