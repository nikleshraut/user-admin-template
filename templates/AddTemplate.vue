<template>
    <div style="padding: 10px 0;">
        <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
        </div>
        <!-- Lab Form -->
        <form class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">
            <!-- modal-body -->
            <div class="modal-body lab-modal-body">
                <div v-show="error" class="col-sm-12">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>
                <div v-if="lab_form.type !=1 && lab_form.type !=2">
                    <div class="row panel">
                        <div class="col-md-9"><h4>Add/Edit Template</h4></div>
                        <div class="col-md-3 pull-right padding-tb"><span class="required_field"> *</span> denotes required field</div>
                    </div>

                    <!-- Template Title -->
                    <div class="row">
                        <label for="name" class="col-md-2 control-label">Name<span class="required_field">*</span></label>
                        <div class="col-md-4 form-group">
                            <input class="form-control" placeholder="Template title" v-model="lab_form.name" type="text" required="">
                            <div v-bind:class="{ 'text-danger': errors.name }">{{errors.name?errors.name[0]:''}}
                            </div>
                        </div>
                        <label for="is_default" class="col-md-2 control-label">Defualt Template<span class="required_field">*</span></label>
                        <div class="col-md-4 form-group no-padding">
                            <div class="col-md-4">Yes : <input value="1" type="radio" v-model="lab_form.is_default" required=""></div>
                            <div class="col-md-8">No : <input value="0" type="radio" v-model="lab_form.is_default"></div>
                            <div class="col-md-12" v-bind:class="{ 'text-danger': errors.is_default }">{{errors.is_default?errors.is_default[0]:''}}
                            </div>
                        </div>
                    </div>
                    <!-- Template Type and Default -->
                    <div class="row">
                        <label for="paper_size" class="col-md-2 control-label">Page Size<span class="required_field">*</span></label>
                        <div class="col-md-4 form-group no-padding">
                            <div class="col-md-4">A4 : <input id="default_template_yes" value="1" type="radio" v-model="lab_form.paper_size" required=""></div>
                            <div class="col-md-8">Letter : <input id="default_template_no" value="0" type="radio" v-model="lab_form.paper_size"></div>
                            <div class="col-md-12" v-bind:class="{ 'text-danger': errors.paper_size }">{{errors.paper_size?errors.paper_size[0]:''}}
                            </div>
                        </div>
                        <label for="orientation" class="col-md-2 control-label">Orientations<span class="required_field">*</span></label>
                        <div class="col-md-4 form-group no-padding">
                            <div class="col-md-4">Portrait : <input value="1" type="radio" v-model="lab_form.orientation" required=""></div>
                            <div class="col-md-8">Landscape : <input value="0" type="radio" v-model="lab_form.orientation"></div>
                            <div class="col-md-12" v-bind:class="{ 'text-danger': errors.orientation }">{{errors.orientation?errors.orientation[0]:''}}
                            </div>
                        </div>
                    </div>
                    <!-- Template Margins -->
                    <div class="row">
                    <label for="name" class="col-md-2 control-label">Margins<span class="required_field">*</span></label>
                        <div class="col-md-10 form-group no-padding">
                            <div class="col-md-2">
                                <input class="form-control" placeholder="Top" v-model="lab_form.margins.top" type="text" required="">
                            </div>
                            <div class="col-md-2">
                                <input class="form-control" placeholder="Right" v-model="lab_form.margins.right" type="text" required="">
                            </div>
                            <div class="col-md-2">
                                <input class="form-control" placeholder="Bottom" v-model="lab_form.margins.bottom" type="text" required="">
                            </div>
                            <div class="col-md-2">
                                <input class="form-control" placeholder="Left" v-model="lab_form.margins.left" type="text" required="">
                            </div>
                        </div>
                    </div>
                </div>
                <div class="row" v-if="lab_form.type != 2">
                    <div class="col-sm-6" style="font-size: 20px;margin: 10px 0;">
                        Template Header
                    </div>
                    <div class="col-sm-6" style="margin: 10px 0;" v-if="lab_form.type == 1">
                        <select v-model="lab_form.header_field" v-on:change="getReference(lab_form.header_field,'template_header')" class="form-control">
                            <option value="">Select Header Field</option>
                            <option v-for="option in header_fields" :value="option.value">{{option.label}}</option>
                        </select>
                    </div>
                </div>
                <!-- Template Header -->
                <div class="row" v-if="lab_form.type != 2">
                    <div class="col-md-12">
                        <textarea name="template_html" id="template_header" rows="10" cols="80"> {{lab_form.header_html}}
                        </textarea>
                    </div>
                </div>
                <div class="row" v-if="lab_form.type !=1 && lab_form.type !=2">
                    <div class="col-sm-6" style="font-size: 20px;margin: 10px 0;">
                        Template Content
                    </div>
                    <div class="col-sm-6" style="margin: 10px 0;">
                        <select v-model="lab_form.report_field" v-on:change="getReference(lab_form.report_field,'template_html')" class="form-control">
                            <option value="">Select Report Field</option>
                            <option v-for="option in report_fields" :value="option.value">{{option.label}}</option>
                        </select>
                    </div>
                </div>
                <!-- Template Content -->
                <div class="row" v-if="lab_form.type !=1 && lab_form.type !=2">
                    <div class="col-md-12">
                        <textarea name="template_html" id="template_html" rows="10" cols="80"> {{lab_form.html}}
                        </textarea>
                    </div>
                </div>
                <div class="row" v-if="lab_form.type != 1">
                    <div class="col-sm-6" style="font-size: 20px;margin: 10px 0;">
                        Template Footer
                    </div>
                    <div class="col-sm-6" style="margin: 10px 0;" v-if="lab_form.type == 2">
                        <select v-model="lab_form.footer_field" v-on:change="getReference(lab_form.footer_field,'template_footer')" class="form-control">
                            <option value="">Select Footer Field</option>
                            <option v-for="option in footer_fields" :value="option.value">{{option.label}}</option>
                        </select>
                    </div>
                </div>
                <!-- Template Footer -->
                <div class="row" v-if="lab_form.type != 1">
                    <div class="col-md-12">
                        <textarea name="template_html" id="template_footer" rows="10" cols="80"> {{lab_form.footer_html}}
                        </textarea>
                    </div>
                </div>
            </div>
            <!--/ modal-body -->

            <!-- modal-footer -->
            <div class="text-right col-md-12">
                <button v-if="lab_form.type !=1 && lab_form.type !=2" v-on:click="getPdf" type="button" class="btn btn-primary" data-toggle="modal" data-target="#pdfModal">Preview</button>
                <button v-on:click="allTemplates" type="button" class="btn btn-default">Cancel</button>
                <button type="submit" class="btn btn-primary">Submit</button>
            </div>
            <div class="col-md-12" v-if="lab_form.type == 1">
                <div class="col-sm-3">
                    <label class="btn btn-default btn-file">
                        Upload image
                        <input type="file" role="button" id="image" v-on:change="uploadImage" style="display: none;">
                    </label>
                </div>
                <div class="col-sm-3">
                    <input class="btn btn-default" v-on:click="getTemplateImages" type="button" data-toggle="modal" data-target="#getImages" value="Get Images">
                </div>
            </div>
            <div class="col-md-12" v-if="lab_form.type == 1">
                <div class="col-sm-6">
                    {{imageUrl}}
                </div>
            </div>
            <!--/ modal-footer -->
        </form>
        <!-- Lab Form -->
<!-- Modal -->
<div class="modal fade" id="getImages" tabindex="-1" role="dialog" aria-labelledby="getImagesLabel">
  <div class="modal-dialog1 col-sm-10 col-sm-offset-1" role="document">
    <div class="modal-content" style="top: 30px;">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="getImagesLabel">Available Images</h4>
      </div>

    <div class="col-sm-12 form-group">
        Copy image url and paste in image popup in template
    </div>
    <div v-for="image in userImages" class="col-sm-12 form-group">
        <img v-bind:src="image" style="width:100px;">  {{image}}
    </div>

      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
      </div>
    </div>
  </div>
</div>
<!-- End of modal -->
<!-- Modal -->
<div class="modal fade" id="pdfModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
  <div class="modal-dialog1 col-sm-10 col-sm-offset-1" role="document">
    <div class="modal-content" style="top: 30px;">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="myModalLabel">Report Preview</h4>
      </div>
      <div class="modal-body">
        <div class="text-center loader showloader">
            <img src="/images/loading.gif">
        </div>
        <object id="pdfviewer" data="" type="application/pdf" style="width:100%;height:600px;">
        </object>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
      </div>
    </div>
  </div>
</div>
<!-- End of modal -->
    </div>
</template>

<script>
    export default {
        mounted() {
            this.$parent.page_title = "Add/Edit Template";
            if(this.$route.params.id != undefined){
                this.updateTemplate(this.$route.params.id);
            }else{
                this.applyEditor();
            }
        },
        data: function() {
            return {
                showloader: false,
                error: {},
                errors: {},
                lab_form: {
                        element_reference:"",default_template:0,
                        margins:{top:0.5,right:0.5,bottom:0.5,left:0.5},
                        html:""
                    },
                types:[{key:"",value:"Select Type"},{key:"1",value:"Header"},{key:"2",value:"Footer"},{key:"3",value:"Invoice"}],
                elements:[{key:"name",value:"Name"},{key:"age",value:"Age"},{key:"city",value:"City"}],
                quill : {},
                report_fields:[
                        {value:"patient_name",label:"Patient Name"},
                        {value:"patient_age",label:"Patient Age"},
                        {value:"patient_address",label:"Patient Address"},
                        {value:"test",label:"Test Name"},
                        {value:"ifNormal",label:"If result is normal"},
                        {value:"ifHigh",label:"If result is high"},
                        {value:"ifLow",label:"If result is low"},
                        {value:"refRanges",label:"Reference Range"},
                    ],
                header_fields:[
                        {value:"lab_name",label:"Lab Name"},
                        {value:"lab_contact",label:"Lab Phone Number"},
                        {value:"lab_address",label:"Lab Address"},
                    ],
                footer_fields:[
                        {value:"lab_name",label:"Lab Name"},
                        {value:"lab_contact",label:"Lab Phone Number"},
                        {value:"lab_address",label:"Lab Address"},
                    ],
                imageUrl:"",
                userImages:[]
            }
        },
        methods:{
            allTemplates: function() {
                location.href = '#/templates';
            },
            submitForm: function() {
                this.showloader =  true;
                var html = 'template_html';
                if(this.lab_form.type == 1){
                    html = 'template_header';
                }else if(this.lab_form.type == 2){
                    html = 'template_footer';
                }
                this.lab_form.html = CKEDITOR.instances[html].getData();
                if(this.$route.params.id)
                    var routes = this.$http.put('/templates/'+this.$route.params.id,this.lab_form);
                else
                    var routes = this.$http.post('/templates',this.lab_form);

                routes.then((response) => {
                    this.showloader =  false;
                    if(response.body.isvalid){
                        if(response.body.success){
                            this.error = {'type':'text-success' ,'message':'Updated successfully'};
                            this.getAllTemplates();
                        }else{
                            this.error = {'type':'text-danger' ,'message':response.body.message};
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            updateTemplate: function(template_id) {
                this.showloader =  true;
                this.$http.get('/templates/'+template_id+'/edit').then((response) => {
                    this.showloader =  false;
                    this.lab_form = response.body.template;
                    console.log(this.lab_form);
                    this.lab_form.margins = JSON.parse(this.lab_form.margins);
                    //this.lab_form.html = 'This is test';
                    //$("#template_html").html(this.lab_form.html);

                    //this.quill.clipboard.dangerouslyPasteHTML(0, this.lab_form.html);
                    var vm = this;
                    setTimeout(function() {
                        vm.applyEditor();
                    }, 100);
                }, (response) => {
                    console.log(response);
                });
            },
            getReference1: function(element) {
                var reference_text = "{{"+$("#elements").val()+"}}";
                $("#element_reference").val("{{"+$("#elements").val()+"}}");
                var range = this.quill.getSelection();
                if (range) {
                  if (range.length == 0) {
                    console.log('User cursor is at index', range.index);
                    this.quill.insertText(range.index,reference_text);
                  } else {
                    var text = this.quill.getText(range.index, range.length);
                    console.log('User has highlighted: ', text);
                    this.quill.updateContents({
                      ops: [
                        { retain: range.index },    // Keep 'Hello '
                        { delete: range.length },   // 'World' is deleted
                        { insert: reference_text }  // Insert 'Quill'
                      ]
                    });
                  }
                } else {
                    this.errors = {html_form:['User cursor is not in editor']};
                    console.log('User cursor is not in editor');
                    //this.quill.insertText(0,reference_text);
                }
            },
            getAllTemplates: function() {
                this.$http.get('/templates').then((response) => {
                    console.log(response.body);
                    this.showloader =  false;
                    this.$parent.templates = response.body.templates;
                    location.href = '#/templates';
                }, (response) => {
                    console.log(response);
                });
           },
           getPdf: function(){
            $("#pdfviewer").attr("data","");
            $(".showloader").show();
            var data = {
                type:$("#type").val(),
                header_html:CKEDITOR.instances.template_header.getData(),
                template_html:CKEDITOR.instances.template_html.getData(),
                footer_html:CKEDITOR.instances.template_footer.getData(),
                page_size: $("#letter").prop("checked") ? "letter":"a4",
                orientation:$("#landscape").prop("checked") ? "landscape":"portrait",
                margin:$("#margin_top").val()+" "+$("#margin_right").val()+" "+$("#margin_bottom").val()+" "+$("#margin_left").val()+";",
                _token:window.Laravel.csrfToken
            }
            $.ajax({
                type: "POST",
                url: "/template-preview",
                data: data
            }).done(function( response ) {
                $(".showloader").hide();
                $("#pdfviewerarea").removeClass("hide");
                $("#pdfviewer").attr("data","/files/sample.pdf");
                $("#pdfviewerarea").focus();
            });
           },
            applyEditor: function(){
                if(this.lab_form.type == 1){
                    CKEDITOR.replace( 'template_header', {height: 300,allowedContent:true} );
                }else if(this.lab_form.type == 2){
                    CKEDITOR.replace( 'template_footer', {height: 300,allowedContent:true} );
                }else{
                    CKEDITOR.replace( 'template_header', {height: 100,readOnly: true/*,removePlugins: 'toolbar'*/} );
                    CKEDITOR.replace( 'template_html', {height: 300,allowedContent:true} );
                    CKEDITOR.replace( 'template_footer', {height: 100,readOnly: true/*,removePlugins: 'toolbar'*/} );
                }
            },
            getReference: function(ref,ref_for){
                var reference_text = "\{\{"+ref+"\}\}";
                CKEDITOR.instances[ref_for].insertText(reference_text);
            },
            uploadImage: function(){
                var imagefile = document.querySelector('#image');
                var formData = new FormData();
                if (imagefile.files && imagefile.files[0]) {
                    formData.append("image", imagefile.files[0]);
                }
                this.$http.post('/upload_template_image',formData).then((response) => {
                    this.showloader = false;
                    if(response.body.success){
                        this.imageUrl = response.body.image_url;
                    }else{
                        this.imageUrl = response.body.message;
                    }

                }, (response) => {
                    console.log(response);
                });
            },
            getTemplateImages : function() {
                this.userImages = [];
                this.$http.get("/get_template_image").then((response) => {
                    var vm = this;
                    var images = JSON.parse(response.body.images);
                    var root = location.protocol + '//' + location.host;
                    setTimeout(function() {
                        for (var i = 0; i < images.length; i++) {
                            var url = root + "/images/users/"+decodeURI(images[i]);
                            vm.userImages.push(url);
                        }
                    }, 500);

                }, (response) => {
                    console.log(response);
                });
            }

        }
    }

</script>