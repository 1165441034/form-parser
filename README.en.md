# Form-Parser

#### Description
Form Generator The custom form echo component before the JSON format rewrite, and the adapted JSON format is also the frame data before the update

#### Software Architecture
Main components Parser  Preview.htmlIt's soy sauce

#### Installation

1.  Import as a component in VUE
2. Use directly in the file

   

#### Instructions

1. The component needs to pass two required values:

   ```
   * formConf: {
   *          type: Object,
   *          required: true
   *       },
   * drawingList: {
   *          type: Array,
   *           required: true
   *      }
   ```

   The data header submitted by the component can be controlled in the custom form design page.

   The submission event is a custom event @submit

   Table data echoes and single row data echoes to the custom form components when editing rows are here

