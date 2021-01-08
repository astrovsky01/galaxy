<template>
    <div id="new_user_welcome">
        <div id="header">
            <b-row class="text-center">
                <h1 class="w-100">{{ new_user_intro.title }}</h1>
                <br/>
                <h4> {{ new_user_intro.intro }}</h4>
            </b-row>
            <br/>
        </div>
        <div id="main_menu">
            <!-- Main and topic menus -->
            <b-tabs card v-model="activeTab" v-if="menuCard==0">
                <b-tab title="Main Menu">
                    <b-card class="text-center">
                        <b-card-header>
                        <h1><strong>Get to Know Galaxy</strong></h1>
                        <br/>
                        </b-card-header>
                        <br/>
                        <b-row class="justify-content-md-center">
                            <div v-for="(subject, index) in new_user_subjects">
                                <b-card-group>
                                    <b-card class="text-center" body-class="d-flex flex-column" style="width: 18rem;">
                                            <b-card-header class="text-center"> 
                                                <h3>
                                                    {{ subject.topic }}
                                                </h3>
                                            </b-card-header>
                                        <b-card-img height="300vh" :src="imgUrl(subject.image)" alt="subject.alt"></b-card-img>
                                        <br/>
                                        <b-card-text>{{ subject.blurb }}</b-card-text>
                                        <b-button class="mt-auto" variant="primary" @click="showPanel(index + 1)">Learn more</b-button>
                                    </b-card>
                                </b-card-group>
                            </div>
                        </b-row>
                    </b-card>
                </b-tab>
                <div v-for="(subject, subject_index) in new_user_subjects">
                    <b-tab :title="subject.topic">
                        <b-card class="text-center">
                            <b-card-header>
                                <h1><strong>{{ subject.topic }} in Galaxy</strong></h1>
                                <br/>
                                <h4>
                                    {{ subject.intro }}
                                </h4>
                            </b-card-header>
                            <b-row class="justify-content-md-center">
                                <div v-for="(subtopic, subtopic_index) in subject.subtopics">
                                    <b-card-group style="height: 30rem">
                                        <b-card class="text-center" body-class="d-flex flex-column" style="width: 15rem;">
                                            <b-card-header style="height: 6rem; text-align: center; align-v: bottom;">
                                                <h4>
                                                    {{ subtopic.title }}
                                                </h4>
                                            </b-card-header>
                                            <br/>
                                            <b-card-img height="200vh" :src="imgUrl(subtopic.image)"></b-card-img>
                                            <br/>
                                            <b-card-text>{{ subtopic.intro }}</b-card-text>
                                            <b-button class="mt-auto" variant="primary" @click="showTopic(subject_index); showCarousel(subtopic_index); menuCard=1;">Learn more</b-button>
                                        </b-card>
                                    </b-card-group>
                                </div>
                            </b-row>
                            <br/>
                            <b-card-footer>
                                <b-button class="mt-auto" variant="primary" @click="showPanel(0); menuCard = 0;">Return</b-button>
                            </b-card-footer>
                        </b-card>
                    </b-tab>
                </div>
            </b-tabs>
        </div>

        <div id="carousels">
            <b-tabs card v-model="topicTab" v-if="menuCard==1"> 
                <div  v-for="(subject, i) in new_user_subjects">
                    <!-- Tabs for major topics -->
                    <b-tab :title="subject.title">
                        <!-- Tabs for carousel topics -->
                        <b-tabs v-model="carouselTab">
                            <div v-for="(subtopic, sub_index) in subject.subtopics">
                                <b-tab :title="subtopic.title">
                                    <b-card class="text-center">
                                        <b-card-header>
                                            <h4>
                                                {{ subtopic.header }}
                                            </h4>
                                        </b-card-header>
                                        <b-carousel id="data-importer" 
                                            controls
                                            indicators
                                            :interval="0"
                                            no-animation
                                            align='center'
                                        >
                                            <div v-for="slide in subtopic.slides">
                                                <b-carousel-slide :class="slide[1]" :img-src="imgUrl(slide[0])" :alt="slide[2]">
                                                    <h4>{{ slide[3] }}</h4>
                                                    </br>
                                                </b-carousel-slide>    
                                            </div>
                                            <b-carousel-slide class="large-img" :img-src="imgUrl('static/images/new_user_welcome/galaxy_logo.png')" img-alt="Galaxy logo"> 
                                                <h4>Enjoy using Galaxy!</h4>
                                                <br/>
                                            </b-carousel-slide>
                                        </b-carousel>
                                        <b-card-footer>
                                            <b-button class="mt-auto" variant="primary" @click="menuCard = 0; showPanel(i+1);">Return</b-button>
                                        </b-card-footer>
                                    </b-card>
                                </b-tab>
                            </div>  
                        </b-tabs>
                    </b-tab>
                </div>
            </b-tabs>
        </div>
    </div>


</template>
<script>
import { BCard, BCardGroup, BTabs, BTab, BCarousel, BCarouselSlide, BButton, BRow, BCol } from "bootstrap-vue";
import { getAppRoot } from "onload/loadConfig"

export default {
    components: { BCard, BCardGroup, BTabs, BTab, BCarousel, BCarouselSlide, BButton, BRow, BCol },
    data() {
        return {
            activeTab: -1, /*Which tab is active on the main card*/
            menuCard: 0, /*Which card is visible*/
            topicTab: -1, /*Which topic is shown in the second card*/
            carouselTab: 0, /*Which slideshow is being shown*/
            new_user_intro: {
                title: "Welcome to Galaxy",
                intro: "Galaxy is web-based platform for reproducible computational analysis. Research in Galaxy \
                    is supported by 3 pillars: data, tools, and workflows. For an introduction to each, visit the \
                    below pages, or begin your analysis by selting a tool from the toolbar to the left."
            },
            new_user_subjects: [
                {
                    topic: "Data",
                    image: "static/images/new_user_welcome/database-solid.svg",
                    blurb: "How to get datasets into Galaxy, and modify them once they're imported.",
                    intro: "Obtaining data in Galaxy is simple. You may upload from your machine, retrieve from a url, via ftp, or from any of several \
                            linked databases. Further, you may use shard data by accessing our ‘shared data libraries’ and ‘shared histories’.",
                    subtopics: [
                        {
                            title: "Importing via Data Uploader",
                            header: "Galaxy Data Uploader",
                            image: "static/images/new_user_welcome/upload-solid.svg",
                            alt: "Data Uploader",
                            intro: "Loading in data from your machine or via URL.",
                            slides: [
                                // [Image, size, alt, text]
                                ["static/images/new_user_welcome/data/data_importer/importer_button.png", "small-img", "Import Button Location", "The upload button can be found on the top of the toolbar to the left of the screen."],
                                ["static/images/new_user_welcome/data/data_importer/importer_icon.png", "mini-img", "Import button symbol", "Click the upload icon to bring up the data importer."],
                                ["static/images/new_user_welcome/data/data_importer/importer_modal.png", "large-img", "Import modal", "The data importer is made up of a central pane, a set of import methods, mechanisms, and metadata inputs."],
                                ["static/images/new_user_welcome/data/data_importer/local_button.png", "small-img", "Import data from local machine", "Click the 'Choose local files' button to select files from your machine."],
                                ["static/images/new_user_welcome/data/data_importer/ftp_button.png", "small-img", "ftp-based upload button", "Click the 'Choose FTP files' button to import files via ftp."],
                                ["static/images/new_user_welcome/data/data_importer/url_button.png", "small-img","Url-based upload button", "Click the 'Paste/Fetch data' button to input a url for a dataset or a text to be uploaded as a file by itself."],
                                ["static/images/new_user_welcome/data/data_importer/paste_field.png", "med-img", "Url paste field", "After clicking the 'Paste/Fetch data' button, paste text or a url in the field to specify data."],
                                ["static/images/new_user_welcome/data/data_importer/pasted_data.png", "med-img", "Pasted data in the url input", "You may also put multiple URLs at once, one per line."],
                                ["static/images/new_user_welcome/data/data_importer/metadata_fields.png", "large-img", "Manually set metadata on upload", "Set the metadata of file type or genome assembly on upload for each file individually or for all at once."],
                                ["static/images/new_user_welcome/data/data_importer/start_button.png", "mini-img", "Start import button", "Click start to begin the data import."],
                                ["static/images/new_user_welcome/data/data_importer/rule_based.png", "large-img", "Rule-based uploader", "Alternatively, the rule-based uploader can be used to upload many files at once, or data from a tabular file."],
                                ["static/images/new_user_welcome/data/data_importer/rule_paste_field.png", "large-img","Pasted data in the rule-based uploader", "Simply paste your tabular data into the rule-based text input and click build."],
                                ["static/images/new_user_welcome/data/data_importer/rule_column.png", "med-img", "Set rules in the left column", "Set rules to be applied to the table in the left column."],
                                ["static/images/new_user_welcome/data/data_importer/set_rules.png", "large-img", "Data appears in the history once imported", "Once your data begins importing, it will appear in the history column to the right. It will appar grey while the import is pending, yellow while it is running, red if there is an error, and green when complete and ready to be used in an analysis."]
                            ]
                        },
                        {
                            title: "Retrieving Data from Public Databases",
                            header: "Importing from Public Databases in Galaxy",
                            image: "static/images/new_user_welcome/cloud-download-alt-solid.svg",
                            alt: "Database Data",
                            intro: "Use data from major sources such as UCSC, SRA, or EMBL.",
                            slides: [
                                ["static/images/new_user_welcome/data/databases/get_data_row.png", "med-img", "Get Data Row", "To retrieve data from a linked database, click on the 'Get Data' row in the toolbar to the left."],
                                ["static/images/new_user_welcome/data/databases/expanded_get_data.png", "med-img", "Expanded Get Data Section", "The expanded get data section shows the full list of integrated databases, accessibly by clicking them here."],
                                ["static/images/new_user_welcome/data/databases/data_tool_form.png", "large-img", "Data tool form", "Many of these tools use a standard Galaxy tool form to access the data. Input the requested data here according to the help sections for each of these tools request files."],
                                ["static/images/new_user_welcome/data/databases/UCSC.png", "large-img", "Database pages", "Other databases, such as the UCSC data browser, are integrated in such a way as to temporarily redirect you directly to their interface."],
                                ["static/images/new_user_welcome/data/databases/UCSC_export.png", "large-img", "Database export", "Once the database's forms have been filled out, you will have the option to export data to galaxy. Click that button to complete the request and return to the Galaxy home page."],
                                ["static/images/new_user_welcome/data/databases/imported_data.png", "med-img", "Imported data", "All data imported in these methods will appear in your history and turn green when fully downloaded and ready to use."]
                            ]
                        },
                        {
                            title: "Obtaining Shared Data",
                            header: "Galaxy Shared Data",
                            image: "static/images/new_user_welcome/slideshare-brands.svg",
                            alt: "Shared data",
                            intro: "Find and retrieve data shared between Galaxy users.",
                            slides: [
                                ["static/images/new_user_welcome/data/shared/masthead.png", "large-img", "Galaxy masthead", "The shared data tab is accessible from the masthead"],
                                ["static/images/new_user_welcome/data/shared/shared_tab.png", "small-img", "Shared data tab", "From the drop down, users have acces to both the data libraries and histories shared across users on the server."],
                                ["static/images/new_user_welcome/data/shared/data_library.png", "med-img", "Data library page", "Clicking the data library row will show you a list of all avalilable shared data libraries."],
                                ["static/images/new_user_welcome/data/shared/library_directory.png", "large-img", "Data library subdirectories", "Many shared data libraries have sub-directories. You can select entire directories to import."],
                                ["static/images/new_user_welcome/data/shared/library_files.png", "large-img", "Data library files", "By clicking on directories, you can choose individual files to import."],
                                ["static/images/new_user_welcome/data/shared/select_files.png", "large-img", "Select shared files to import", "Use the checkboxes to the left to choose which files or directories to import."],
                                ["static/images/new_user_welcome/data/shared/export_library.png", "small-img", "Export to history button", "Click the 'Export to History' button to select the format to retrieve selected data."],
                                ["static/images/new_user_welcome/data/shared/choose_history.png", "large-img", "Choose existing or new history for files.", "Having selected format, either choose a new or existing history into which to import the data and click 'Import'"],
                                ["static/images/new_user_welcome/data/shared/history_view.png", "med-img", "Data appears in the selected history", "Imported data will appear in the specified history."],
                                ["static/images/new_user_welcome/data/shared/published_history.png", "large-img", "Shared history view", "Alternatively, you can access the published histories from the same tab on the masthead."],
                                ["static/images/new_user_welcome/data/shared/published_view.png", "med-img", "Shared history file list", "Selecting a history, all availailable files are available to view and search in the same format as the normal history bar."],
                                ["static/images/new_user_welcome/data/shared/published_import.png", "med-img", "About this history. Click plus to import.", "Information on the history is availalbe in the column on the right. To import this data, click the plus botton at the top right of the column."],
                                ["static/images/new_user_welcome/data/shared/name_history.png", "large-img", "Name history for data import.", "Name the history for the imported files, then click import. As before, the data will appear in the specified history."]
                            ]
                        },
                        {
                            title: "Data and Metadata in Galaxy.",
                            header: "Galaxy Data and Metadata",
                            image: "static/images/new_user_welcome/info-circle-solid.svg",
                            alt: "Observing Data and Metadata",
                            intro: "Observing and modifying your data",
                            slides: [
                                ["static/images/new_user_welcome/data/observe/history_panel.png", "med-img", "Galaxy history panel", "The history panel to the right of the screen shows all datasets and files used in the current analysis. You can create a new history or switch to a previous one using the buttons at the top."],
                                ["static/images/new_user_welcome/data/observe/expanded_dataset.png", "med-img", "Expanded dataset view", "Click a dataset in the history to expand it. Expanded items have a quick view pane for the associated dataset as well as more options on how to interact with the history item."],
                                ["static/images/new_user_welcome/data/observe/view_data.png", "large-img", "View file", "Click the eye to open the file as readible in the center panel."],
                                ["static/images/new_user_welcome/data/observe/data_info.png", "large-img", "File info", "Click the 'i' icon to view information about the dataset and the job/tool/command that created it."],
                                ["static/images/new_user_welcome/data/observe/metadata.png", "large-img", "Metadata page", "Clicking the pencil icon will take you to the metadata page."],
                                ["static/images/new_user_welcome/data/observe/set_metadata.png", "large-img", "Edit metadata", "From the Attributes tab, you can edit metadata such as the genome assembly of the data, and add notes/anotations."],
                                ["static/images/new_user_welcome/data/observe/convert_dataset.png", "large-img", "Data format conversion", "The Convert tab allows you to run basic tools to convert between similar datatypes (i.e. txt and tsv)."],
                                ["static/images/new_user_welcome/data/observe/convert_datatype.png", "large-img", "Data format reset", "On the Datatypes tab, you can manually set the datatype without altering the file, if, for example, the automatic filetype detector failed to set properly (such as a tsv file set as a txt)."]
                            ]
                        
                        }
                    ]
                },
                {
                    topic: "Tools",
                    image: "static/images/new_user_welcome/toolbox-solid.svg",
                    alt: "Tools",
                    blurb: "Analysis in Galaxy using computational tools.",
                    intro: "Tools in Galaxy are the same as those one would run locally for a traditional analysis, \
                            save for a couple details. Tools are run in a Galaxy environment, which is identical to \
                            other Galaxy environments, making reproducibility simple. They are also run on Galaxy \
                            servers with appropriate processing power, so researchers don’t have to worry about their \
                            machine’s capabilities.",
                    subtopics: [
                        {
                            title: "Standard Tools and the Tool Form",
                            header: "Tool forms in Galaxy",
                            image: "static/images/new_user_welcome/tools-solid.svg",
                            alt: "Tool form",
                            intro: "Understanding the tool form and runing any standard tool",
                            slides: [
                                ["static/images/new_user_welcome/tools/tool_form/tool_panel.png", "med-img", "Galaxy toolbar", "Most galaxy tools are accessible via the toolbar to the left of the center panel."],
                                ["static/images/new_user_welcome/tools/tool_form/tool_form.png", "large-img", "Galaxy tool form", "After clicking to expand tool sections, you can select a tool to display its tool form in the center panel. Tool forms have several methods of inputs which will be detailed in following slides."],
                                ["static/images/new_user_welcome/tools/tool_form/data_input.png", "large-img", "Data inputs", "At the top of most tool forms, there is a data input section. To input a file, you can click and drag in input file from the history, or click the drop down and select an input."],
                                ["static/images/new_user_welcome/tools/tool_form/multi_data.png", "mini-img", "Multiple data inputs inputs", "You can also select multiple datasets or a collections using the three buttons to the left."],
                                ["static/images/new_user_welcome/tools/tool_form/string_input.png", "large-img", "String inputs", "Text-based inputs provide text boxes that allow you to input a text string, an integer, or a decimal value depending on the tool."],
                                ["static/images/new_user_welcome/tools/tool_form/select_input.png", "large-img", "Select inputs", "Select inputs allow you to choose from specified options. Sometimes new options will appear on the form depending on which option you choose."],
                                ['static/images/new_user_welcome/tools/tool_form/select_box_input.png', "large-img", "Alternative select inputs", "Select inputs can also be provided in the form of bubble or checkbox menus."],
                                ["static/images/new_user_welcome/tools/tool_form/binary_input.png", "small-img", "Binary inputs", "Binary inputs are shown as buttons and are usually simply on/off parameters in tools. Like with select inputs, more options can appear depending on your choice."],
                                ["static/images/new_user_welcome/tools/tool_form/email_notify.png", "small-img", "Email notifications", "For jobs that can take a long time to run, you can always choose to be notified on completion via email by setting the boolean input at the bottom of the tool form."],
                                ["static/images/new_user_welcome/tools/tool_form/execute.png", "small-img", "Starting a job", "When you are ready, click 'Execute' tp run the tool. Datasets will appear in the history as the job runs, and become green when ready to use in further analysis."]
                            ]
                        },
                        {
                            title: "Data Vizualization Tools",
                            header: "Vizualize Data in Galaxy",
                            image: "static/images/new_user_welcome/chart-bar-solid.svg",
                            alt: "Vizualization Tools",
                            intro: "Easily view your data through graphs and other methods.",
                            slides: [
                                ["static/images/new_user_welcome/tools/viz/masthead.png", "large-img", "Galaxy masthead", "To vizualize you data, first click on the 'Vizualizations' tab on the masthead."],
                                ["static/images/new_user_welcome/tools/viz/viz_dropdown.png", "small-img", "Vizualize dropdown menu", "Next, select 'Create Vizulaization'."],
                                ["static/images/new_user_welcome/tools/viz/viz_menu.png", "large-img", "Vizualizations menu", "Galaxy has dozens of methods of vizualization, from basic graphs to protein structures."],
                                ["static/images/new_user_welcome/tools/viz/viz_search.png", "large-img", "Vizualization options search", "Use the search bar at the top to find the vizualization method for the data to be observe."],
                                ["static/images/new_user_welcome/tools/viz/viz_input.png", "large-img", "Vizualizations input data", "Click the requested method, then find the dataset in the drop down menu and click 'Create Vizualization'."],
                                ["static/images/new_user_welcome/tools/viz/setup_1.png", "small-img", "Setting parameters for the vizualization", "The page that comes up will prompt you to set parameters for the figure before showing you the figure. Set these values in the column to the right."],
                                ["static/images/new_user_welcome/tools/viz/setup_2.png", "small-img", "Setting parameters for the Vizualization cont.", "Also specify which parts of the data are to be observed in the second tab in the right column, when applicable."],
                                ["static/images/new_user_welcome/tools/viz/options_and_run.png", "small-img", "Vizualization actions", "You may create the vizualization initially with the check. Once done, you can save these values with the save icon, take an immediate screenshot to save the image, or edit the paramters and recreate the figure with the check mark."],
                                ["static/images/new_user_welcome/tools/viz/viz_done.png", "large-img", "Completed Vizualizations", ">Once run, your figure will appear in the center panel. You don't have to remain on the page after clicking run to complete the figure"],
                                ["static/images/new_user_welcome/tools/viz/user_dropdown.png", "small-img", "View old Vizualizations", ">If you wish to return and view previous vizualizations, simply go the 'User' tab in the masthead and select 'Vizualizations'."],
                                ["static/images/new_user_welcome/tools/viz/view_old.png", "large-img", "Vizualizations list", "Here, you may click to return to the editor/viewer for any vizualization previously run."]
                            ]
                        }
                    ]
                },
                {
                    topic: "Workflows",
                    image: "static/images/new_user_welcome/project-diagram-solid.svg",
                    alt: "Workflows",
                    blurb: "Running full analyses in Galaxy with workflows.",
                    intro: "Workflows are Galaxy’s chief mechanism for reproducibility. They allow a user to exactly recreate entire analyses they or \
                            apeer have performed previously,  or simply assemble a pipeline from the ground up. Further, these workflows can be \
                            edited to tailor previous analysis parameters to new data.",
                    subtopics: [
                        {
                            title: "Extract Workflows from Analyses",
                            header: "Extracting Workflows in Galaxy",
                            image: "static/images/new_user_welcome/angle-double-up-solid.svg",
                            alt: "Workflow Extraction",
                            intro: "Generate Reproducible Workflows from Your Analysis",
                            slides:[
                                ["static/images/new_user_welcome/workflows/extract/run_history.png", "med-img", "Existing analysis in a history", "After perorming an analysis, you can create a Galaxy workflow to recreate it on new data."],
                                ["static/images/new_user_welcome/workflows/extract/history_actions.png", "med-img", "History actions", "Click the gear icon on the top right of the history panel to bring up the history actions menu. Click 'Extract Workflow'."],
                                ["static/images/new_user_welcome/workflows/extract/extract_menu.png", "large-img", "Workflow extraction menu", "The workflow extraction page will appear, from which you can customize your workflow and select which items are included/excluded."],
                                ["static/images/new_user_welcome/workflows/extract/deselect.png", "large-img", "Unselect unwanted history items", "To prevent tools/datasets in the history  from being added in the workflow, simply click their associated checkboxes. You can also select/deselect en masse using the 'check/uncheck' all buttons at the top of the page."],
                                ["static/images/new_user_welcome/workflows/extract/name_workflow.png", "med-img", "Name your workflow", "Name your workflow in the textbox at the top of the page."],
                                ["static/images/new_user_welcome/workflows/extract/name_and_create.png", "large-img", "Extract when ready", "When ready, click 'Create Workflow'."],
                                ["static/images/new_user_welcome/workflows/extract/extract_complete.png", "large-img", "Extraction complete popup", "The blue popup will appear to show the workflow ahs been created."],
                                ["static/images/new_user_welcome/workflows/extract/workflow_button.png", "small-img", "Workflow tab", "To access your workflows, click the 'Workflows' tab in the masthead."],
                                ["static/images/new_user_welcome/workflows/extract/workflow_list.png", "large-img", "Workflow list", "You will find all available workflows, including the newly created one, in the rows on the Workflows page. Click their associated play button to run them."]
                            ]
                        },
                        {
                            title: "Modify Workflows with the Workflow Editor",
                            header: "Galaxy Workflow Editor",
                            image: "static/images/new_user_welcome/edit-solid.svg",
                            alt: "Workflow Editing",
                            intro: "Edit Workflows for New Data or Analyses",
                            slides:[
                                ["static/images/new_user_welcome/workflows/editor/masthead.png", "large-img", "Workflow tab on masthead", "Access the workflows menu from the 'Workflows' tab in the masthead."],
                                ["static/images/new_user_welcome/workflows/editor/workflow_menu.png", "large-img", "Workflow selector menu", "Click the arrow next to the workflow to be run. Alternatively, click the 'Create' botton on the top right to build a workflow from scratch."],
                                ["static/images/new_user_welcome/workflows/editor/dropdown.png", "med-img", "Workflow dropdown", "Select 'Edit' to open the workflow editor."],
                                ["static/images/new_user_welcome/workflows/editor/editor_base.png", "large-img", "Workflow editor", "This is the workflow editor. Here you can see the entirety of your workflow, or zoom in on a specific part using the zoom buttons on the bottom left, and click-and-drag movement controls."],
                                ["static/images/new_user_welcome/workflows/editor/tool_appears.png", "med-img", "Add tool to the workflow", "To add a tool, find it in the toolbar to the left, as you would to run it normally. The tool will appear as a bubble to the left."],
                                ["static/images/new_user_welcome/workflows/editor/move_tool.png", "large-img", "Moving items in the editor", "To move items in the editor, click and drag them to the desired location."],
                                ["static/images/new_user_welcome/workflows/editor/click-and-drag.png", "large-img", "Click and drag connections", "The bubbles next to parameters in steps denote inputs and outputs. To start a connection, click on the output bubble of a step and drag it so the connection line is visible."],
                                ["static/images/new_user_welcome/workflows/editor/attach.png", "large-img", "Attaching tools to workflow", "Attatch the connection to the input of the following step. The output file must be the proper format for the input of the following tool to be connected."],
                                ["static/images/new_user_welcome/workflows/editor/detatch.png", "med-img", "Detatch items in the editor", "To disconnect items in the editor, simply click the input bubble on the step."],
                                ["static/images/new_user_welcome/workflows/editor/delete.png", "med-img", "Delete or duplicate items in the editor", "To delete a step, click the 'X' at the top left. The icon next to that will duplicate the steps's tool bubble."],
                                ["static/images/new_user_welcome/workflows/editor/params.png", "large-img", "Edit tool parameters in the workflow editor", "To change the parameters of a tool here, click the tool's bubble and edit as with a tool form in the menu that appears to the right."],
                                ["static/images/new_user_welcome/workflows/editor/editor_menu.png", "med-img", "Editor menu options", "When ready, you may save the workflow as-is with the save button on the top left. The other buttons here allow you to edit the workflow metadata (i.e. rename it, annotate, etc.) or download the file, or run the workflow immediately."]
                            ]
                        },
                        {
                            title: "Importing and Running Workflows",
                            header: "Importing and Running Workflows",
                            image: "static/images/new_user_welcome/file-import-solid.svg",
                            alt: "Importing Workflows",
                            intro: "Obtaining previously-run analyses from yourself or others, and recreating existing analyses.",
                            slides: [
                                ["static/images/new_user_welcome/workflows/run/masthead.png", "large-img", "Workflow tab on masthead", "Go to the 'Workflow' tab on the masthead to begin."],
                                ["static/images/new_user_welcome/workflows/run/workflow_main.png", "large-img", "Workflow list page", "On this page you can view all available workflows. Click the 'Import' button to add a new workflow from a file or a url."],
                                ["static/images/new_user_welcome/workflows/run/add_main.png", "large-img", "Workflow import page", "The import page gives you multiple methods of adding workflows from outside of the instance. This will focus on the URL-based import and the local file import."],
                                ["static/images/new_user_welcome/workflows/run/file_up.png", "large-img", "Upload workflow from local machine", "Clicking the 'Archived Workflow File' selector allows a .ga file into your instance to run the associated workflow using your local file browser."],
                                ["static/images/new_user_welcome/workflows/run/url_paste.png", "large-img", "Pasted Url", "The 'Archived Workflow URL' textbox allows a remote file to be requested fomr an outside site such as Github or Zenodo."],
                                ["static/images/new_user_welcome/workflows/run/import_button.png", "small-img", "Upload button", "When ready, click 'Import Workflow' to make the file available to run."],
                                ["static/images/new_user_welcome/workflows/run/uploaded_file.png", "large-img", "Uploaded workflow", "Imported workflows will become available immediately on the main workflow page. To run a workflow, click the play button at the right side of its row."],
                                ["static/images/new_user_welcome/workflows/run/not_expanded.png", "large-img", "Workflow run page", "The initial screen will show all inputs for the workflow. You can run with standard parameters here by inputting files and clicking 'Run Workflow'. Alternatively, you can click below to expand and view all tools used in the workflow."],
                                ["static/images/new_user_welcome/workflows/run/expanded.png", "large-img", "Expanded workflow run page", "From the expanded workflow view, you can click on any tool's row to expand it and view the parameters."],
                                ["static/images/new_user_welcome/workflows/run/edits.png", "large-img", "Alter parameters in the run page", "Clicking the pencil icon next to parameters of expanded tools will allow you to change them for this run specifically."],
                                ["static/images/new_user_welcome/workflows/run/run_workflow.png", "small-img", "Run the workflow", "When ready, click 'Run Workflow' to start."],
                                ["static/images/new_user_welcome/workflows/run/invocation.png", "large-img", "Workflow invocation", "The workflow invocation page will appear, showing how far the workflow run has progressed."],
                                ["static/images/new_user_welcome/workflows/run/history.png", "med-img", "Workflow data in histories", "Files will populate the history as they become available. The page may now be exited, and the workflow will continue to run."]
                            ]
                        }
                    ]
                }
            ]
        }
    },







    methods: {
        imgUrl(src) {
            const root = getAppRoot();
            const url = `${root}${src}`.replace("//", "/");
            return url;
        },
        showPanel(index) {
            this.activeTab = index;
            console.log("Active Tab:");
            console.log(index);
        },
        showTopic(index) {
            this.topicTab = index;
            console.log("topic Tab:");
            console.log(index);
        },
        showCarousel(index) {
            this.carouselTab = index;
            console.log("carousel Tab:");
            console.log(index);
        },
    },
};

</script>
<style type="text/css">
    .nav-tabs {
        display: none;
    }
    .carousel-caption {
        position: relative;
        left: 0;
        top: 0;
        color: black
    }
    .carousel-item {
        float: none;
        margin-right: auto;
        margin-left: auto;
    }
    .carousel-control-next,
    .carousel-control-prev,
    .carousel-indicators {
        filter: invert(100%);
    }
    #logos img {
        max-width: 100%;
    }
    .carousel-inner-img {
        width: auto;
        height: auto;
    }
    .menu-img {

    }
    .mini-img {
        max-width: 100px;
    }
    .small-img {
        max-width: 300px;
    }
    .med-img {
        max-width: 500px;
    }
    .large-img {
        max-width: 750px;
    }
</style>