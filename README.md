## Quasar App Extension DbForm

### Props
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
      <th>Default</th>
      <th>Required</th>
      <th>Note</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap>model-name</td>
      <td>String</td>
      <td>Model name</td>
      <td></td>
      <td>*</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>value</td>
      <td>Object</td>
      <td>Model value</td>
      <td></td>
      <td>*</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>initial-value</td>
      <td>Object</td>
      <td>Initial value (used for clearing form)</td>
      <td>{}</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>autofocus</td>
      <td>Boolean</td>
      <td>Focus first focusable element</td>
      <td>false</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>before-submit</td>
      <td>Function</td>
      <td>Function call after  after a validation was triggered and before emit submit</td>
      <td></td>
      <td></td>
      <td>async, throw for stop process</td>
    </tr>
    <tr>
      <td nowrap>is-dirty</td>
      <td>Boolean</td>
      <td>Set dirty flag for validations</td>
      <td>false</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>extra-validations</td>
      <td>Object</td>
      <td>Validations object for merge with main validations</td>
      <td>{}</td>
      <td></td>
      <td>override main</td>
    </tr>
    <tr>
      <td nowrap>save-on-submit</td>
      <td>Boolean</td>
      <td>Save model to DB after submit</td>
      <td>false</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>

### Events
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Payload</th>
      <th>Description</th>
      <th>Note</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap>submit</td>
      <td></td>
      <td>Emitted when all validations have passed when tethered to a submit button</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>reset</td>
      <td></td>
      <td>Emitted when form cleared and all validations have been reset when tethered to a reset button</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>cancel</td>
      <td></td>
      <td>Emitted when form reset to loaded value and all validations have been reset when tethered to a cancel button</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>save</td>
      <td>saved value object</td>
      <td>Emitted after model saved to DB</td>
      <td></td>
    </tr>
  </tbody>
</table>

### Methods
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Parameters</th>
      <th>Description</th>
      <th>Note</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap>submit</td>
      <td></td>
      <td>Trigger a submit form</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>reset</td>
      <td></td>
      <td>Trigger a reset form to initial value (clear form) and reset validation status</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>cancel</td>
      <td></td>
      <td>Trigger a reset form to loaded value and reset validation status</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>save</td>
      <td></td>
      <td>Trigger a save model to DB</td>
      <td></td>
    </tr>
  </tbody>
</table>

### Data vars
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
      <th>Note</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap>model</td>
      <td>Model obj</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>notChanged</td>
      <td>Form not changed</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>hasErrors</td>
      <td>Form has errors</td>
      <td></td>
    </tr>
    <tr>
      <td nowrap>loadedValue</td>
      <td>Loaded value</td>
      <td></td>
    </tr>
  </tbody>
</table>


### Slots
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Scope</th>
      <th>Description</th>
      <th>Note</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap>default</td>
      <td></td>
      <td>Main content</td>
      <td></td>
    </tr>
  </tbody>
</table>
