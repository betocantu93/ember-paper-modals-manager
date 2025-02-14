# `progress`

This modal used to show a progress-bar for chain of Promises executed one by one. This modal doesn't have any controls like confirm/decline-buttons in the footer. Modal will be confirmed and self-closed after all promises are fulfilled or it will be declined (and self-closed) if at least one promise becomes rejected.

{{#docs-snippet name="show-progress-modal.js" title="Progress Modal"}}
import Controller from '@ember/controller';
import {inject as service} from '@ember/service';
import {get} from '@ember/object';
import {Promise} from 'rsvp';

export default Controller.extend({
  modalsManager: service(),

  actions: {
    showPromptConfirm() {
      const modalsManager = get(this, 'modalsManager');
      modalsManager
        .progress({
          body: '',
          promises: [ // this is required
            () => new Promise(resolve => setTimeout(resolve(1), 100)),
            () => new Promise(resolve => setTimeout(resolve(2), 100)),
            () => new Promise(resolve => setTimeout(resolve(3), 100))
          ]
        })
        .then(result => {
          // called after chain of `promises` is executed.
          // here "result" is an array of values for fulfilled promises
        })
        .catch(([result, error]) => {
          // called when at least one promise is rejected
          // here "result" is an array of already fulfilled promises
          // here "error" is a reason why last promise was rejected
          return modalsManager
            .alert({
              title: 'Something goes wrong',
              body: `Fulfilled - ${result}. Error - ${JSON.stringify(error)}`
            });
        });
    }
  }
});
{{/docs-snippet}}

`progress` has several extra options:

* `circular` - `true` for `circular` progress-bar, `false` - for `linear` progress-bar (default)
* `circularAccent` - `accent`-binding for `circular` progress-bar (default `false`)
* `circularWarn` - `warn`-binding for `circular` progress-bar (default `false`)
* `circularDiameter` - `diameter`-binding for `circular` progress-bar (default `25`)
*`circularStrokeRatio` - `strokeRadio`-binding for `circular` progress-bar (default `0.1`)
* `linearAccent` - `accent`-binding for `linear` progress-bar (default `false`)
* `linearWarn` - `warn`-binding for `linear` progress-bar (default `false`)
 
**IMPORTANT** Here `options.promises` is a list of _FUNCTIONS_ that returns Promises!
 
Option `options.settled` can be used to force complete all provided `promises` and don't stop on first canceled Promise.
 
Option `options.cancelable` can be used to add ability to stop `promises`-queue.

## Customization

{{#docs-snippet name="show-custom-progress-modal.js" title="Custom Progress Modal"}}
import Controller from '@ember/controller';
import {inject as service} from '@ember/service';
import {get} from '@ember/object';
import {Promise} from 'rsvp';

export default Controller.extend({
  modalsManager: service(),

  actions: {
    showPromptConfirm() {
      const modalsManager = get(this, 'modalsManager');
      modalsManager
        .progress({
          body: '',
          titleComponent: 'custom-progress-header',
          bodyComponent: 'custom-progress-body',
          footerComponent: 'custom-progress-footer',
          promises: [ // this is required
            () => new Promise(resolve => setTimeout(resolve(1), 100)),
            () => new Promise(resolve => setTimeout(resolve(2), 100)),
            () => new Promise(resolve => setTimeout(resolve(3), 100))
          ]
        })
        .then(result => {
          // called after chain of `promises` is executed.
          // here "result" is an array of values for fulfilled promises
        })
        .catch(([result, error]) => {
          // called when at least one promise is rejected
          // here "result" is an array of already fulfilled promises
          // here "error" is a reason why last promise was rejected
          return modalsManager
            .alert({
              title: 'Something goes wrong',
              body: `Fulfilled - ${result}. Error - ${JSON.stringify(error)}`
            });
        });
    }
  }
});
{{/docs-snippet}}

### Title Component

It takes a single parameter `options`. Its value is an object passed to the `modalsManager.progress`.

{{docs-snippet name="components-custom-progress-header.hbs" title="components/custom-progress-header.hbs"}}

### Body Component

It takes four parameters. First one is an options described before. Second one is an `overall` - number of `promises` passed to the modal. Third one is a `done` - number of already fulfilled promises. Fourth one is a `progress` - calculated percentage value of the fulfilled promises that me be used in the progress-bar. Usage example:

{{docs-snippet name="components-custom-progress-body.hbs" title="components/custom-progress-body.hbs"}}

### Footer Component

It takes one parameter called `options`. It was described before. There are no action-handlers because progress-modal is auto-closed and triggers `decline` and `confirm` by itself and not with user's interaction.

{{docs-snippet name="components-custom-progress-footer.hbs" title="components/custom-progress-footer.hbs"}}