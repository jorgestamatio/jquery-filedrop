jQuery filedrop plugin
======================

HTML5 drag desktop files into browser
-------------------------------------

jQuery filedrop uses the HTML5 File API to allow users
to drag multiple files from desktop to the browser, uploading
each file to a user-specified URL.

filedrop uses HTML5 FileReader() to read file data.

Browser Support
---------------
Works on Chrome, Firefox 3.6+, IE10+, and Opera 12+.

Would love contribution for Safari support.

filedrop also allows users to define functions to handle the `BrowserNotSupported` error.

Usage Example
---------------

```javascript
//////////////// filedrop
var dropzone = $('#dropzone'),
		message = $('.message', dropzone),
		upload_url = $('input[name="upload_url"]').val();
		
dropzone.filedrop({
	// The name of the $_FILES entry:
	maxfiles: 5,
  maxfilesize: 20,
	url: upload_url,
	paramname: 'userfile',          // POST parameter name used on serverside to reference file, can also be a function taking the filename and returning the paramname
	withCredentials: false,          // make a cross-origin request with cookies
	data: {
  	  csrf_test_name: $('input[name="csrf_test_name"]').val(),           // send POST variables
	},

	uploadFinished:function(i,file,response){
		console.log(response.status);
		location.reload();
		// response is the JSON object that post_file.php returns
	},

  	error: function(err, file) {
		switch(err) {
			case 'BrowserNotSupported':
				showMessage('Your browser does not support HTML5 file uploads!');
				break;
			case 'TooManyFiles':
				alert('Too many files! Please select 5 at most!');
				break;
			case 'FileTooLarge':
				alert(file.name+' is too large! Please upload files up to 2mb (configurable).');
				break;
			default:
				break;
		}
	},

	// Called before each upload is started
	beforeEach: function(file){
		if(!file.type.match(/^image\//)){
			//alert('Only images are allowed!');

			// Returning false will cause the
			// file to be rejected
			//return false;
		}
	},
	uploadStarted:function(i, file, len){
		createImage(file);
	},

	progressUpdated: function(i, file, progress) {
		$.data(file).find('.progress').width(progress);
	}

});

var template ='<div class="preview">'+
								'<span class="imageHolder">'+
								'<img />'+
								'<span class="uploaded"></span>'+
								'</span>'+
								'<div class="progressHolder">'+
									'<div class="progress"></div>'+
								'</div>'+
							'</div>'; 


function createImage(file){
	var preview = $(template), 
			image = $('img', preview);

	var reader = new FileReader();

	image.width = 100;
	image.height = 100;

	reader.onload = function(e){
		// e.target.result holds the DataURL which
		// can be used as a source of the image:
		image.attr('src',e.target.result);

	};

	// Reading the file as a DataURL. When finished,
	// this will trigger the onload function above:
	reader.readAsDataURL(file);

	message.hide();
	preview.appendTo(dropzone);

	// Associating a preview container
	// with the file, using jQuery's $.data():

	$.data(file,preview);
}

function showMessage(msg){
	message.html(msg);
}


//////////////// filedrop
```

Queueing Usage Example
----------------------

To enable the upload of a large number of files, a queueing option was added that enables you to configure how many files should be processed at a time.  The upload will process that number in parallel, backing off and then processing the remaining ones in the queue as empty upload slots become available.

This is controlled via one of two parameters:

```javascript
maxfiles: 10    // Limit the total number of uploads possible - default behaviour
queuefiles: 2   // Control how many uploads are attempted in parallel (ignores maxfiles setting)
```

Not setting a value for `queuefiles` will disable queueing.

Contributions
-------------

* [Reactor5](http://github.com/Reactor5/) (Brian Hicks)
* [jpb0104](http://github.com/jpb0104)
