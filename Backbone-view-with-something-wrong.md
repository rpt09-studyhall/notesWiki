```javascript
const VideoListEntryView = Backbone.View.extend({

   initialize:  () => {
     $('.video-list-entry-title').on('click', handleClick);
  },

  events: {
    'click .video-list-entry-title': 'handleClick'
  },

  handleClick: () => {
    model.select();
  },

  render: () => {
    $el.html(this.template(this.model.attributes));
    return this;
  },

  template: templateURL('src/templates/videoListEntry.html')

});