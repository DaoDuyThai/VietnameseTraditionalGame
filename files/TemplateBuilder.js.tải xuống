// ELEMENT
var upload_file = null;
var input_color = null;
var deleted_files = "";
var need_to_save = true;

// ***********************************************************************
// START
//
document.addEventListener('DOMContentLoaded', function () {
    if (EditMode == '1') {
        SetUpTemplateControl();
    }
}, false);

// ***********************************************************************
// Hiển thị control và các sự kiện
//
function SetUpTemplateControl() {
    console.log("EDIT CONTROL START");
    // Rê chuột hiển thị control
    $('.HaveTemplateControl').mouseover(function () {
        $(this).find('.TemplateControl').css('display', 'block');
    });
    $('.HaveTemplateControl').mouseout(function () {
        $(this).find('.TemplateControl').css('display', 'none');
    });
    // Check sử dụng (thêm hoặc bỏ class ItemHide)
    $('.CheckUseItem').each(function () {
        var target = $(this).attr('target');
        if ($('#' + target)) {
            if ($('#' + target).hasClass('ItemHide')) {
                $(this).prop('checked', false);
            } else {
                $(this).prop('checked', true);
            }
        }
    });
    $('.CheckUseItem').click(function () {
        need_to_save = true;
        if ($(this).is(':checked')) {
            var target = $(this).attr('target');
            $('#' + target).removeClass('ItemHide');
            $($("#HoldRootContent").contents().find('#' + target)[0]).removeClass('ItemHide');
        } else {
            var target = $(this).attr('target');
            $('#' + target).addClass('ItemHide');
            $($("#HoldRootContent").contents().find('#' + target)[0]).addClass('ItemHide');
        }
    });
    // Ghi nhớ giá trị (TRUE - FALSE) thành (1 - 0)
    $('.TrueOrFalse').each(function () {
        var target = $(this).attr('target');
        if ($('#' + target)) {
            if ($('#' + target).html()=='1') {
                $(this).prop('checked', true);
            } else {
                $(this).prop('checked', false);
            }
        }
    });
    $('.TrueOrFalse').click(function () {
        need_to_save = true;
        if ($(this).is(':checked')) {
            var target = $(this).attr('target');
            $('#' + target).html('1');
            $($("#HoldRootContent").contents().find('#' + target)[0]).html('1');
        } else {
            var target = $(this).attr('target');
            $('#' + target).html('0');
            $($("#HoldRootContent").contents().find('#' + target)[0]).html('0');
        }
    });
    // Văn bản
    $('.TextOfItem').each(function () {
        var target = $(this).attr('target');
        console.log(target);
        if ($('#' + target)) {
            var text = $('#' + target).html();
            $(this).val(text);
        }
    });
    $('.TextOfItem').keyup(function () {
        var text = $(this).val();
        var target = $(this).attr('target');
        $('#' + target).html(text);
        $($("#HoldRootContent").contents().find('#' + target)[0]).html(text);
        need_to_save = true;
    });
}

// Nhận ACTION
function BuilderDoAction(data) {
    // Upload ảnh mới thay ảnh củ
    if (data.action_type == 'upload' && data.object_type == 'image') {
        UploadNewImage(data);
    }
    // Xóa ảnh khỏi slide
    else if (data.action_type == 'remove' && data.object_type == 'image-slide') {
        if (data.mIndex == 1) {
            alert("Không thể xóa hình ảnh duy nhất còn lại trong slide!");
        }
        else if (confirm("Bạn thực sự muốn xóa ảnh đang nhìn thấy trong slide?")) {
            RemoveImageFromSlide(data);
        }
    }
    // Upload thêm ảnh mới vô slide
    else if (data.action_type == 'upload' && data.object_type == 'image-slide') {
        UploadNewImageToSlide(data);
    }
    // Đổi màu chủ đạo
    else if (data.action_type == 'background-color') {
        ChangePrimaryBackgroundColor(data);
    }

    need_to_save = true;
}

// Đổi màu chủ đạo
var FirstColor = true;
function ChangePrimaryBackgroundColor(_data) {
    if (input_color == null) {
        input_color = document.createElement('input');
        input_color.id = "InputColor";
        input_color.type = "color";
        input_color.style.position = "absolute";        
        input_color.style.zIndex = "-1";
        input_color.style.opacity = "0";
        input_color.name = "MyInputColor";
        document.body.appendChild(input_color);
    }
    $('#InputColor').attr('value',rgb2hex($($(_data.target)[0]).css('background-color')));
    $('#InputColor').off('change');
    $('#InputColor').change(function () {        
        $(_data.target).css('background-color', input_color.value);
        $($("#HoldRootContent").contents().find(_data.target)).css('background-color', input_color.value);
    });
    setTimeout("ClickChangeColor()", 500);
    need_to_save = true;
}
function ClickChangeColor() {
    $('#InputColor').click();
    need_to_save = true;
}

// Upload ảnh mới thay ảnh củ (đang là logo)
function UploadNewImage(_data) {    
    if (upload_file == null) {
        upload_file = document.createElement('input');
        upload_file.type = "file";
        upload_file.style.position = "absolute";
        upload_file.style.zIndex = "-1";
        upload_file.style.width = "0px";
        upload_file.style.height = "0px";
        upload_file.style.opacity = "0";
        document.body.appendChild(upload_file);        
    }
    $(upload_file).off('change');
    upload_file.onchange = function () {
        var file = upload_file.files[0];
        var _URL = window.URL || window.webkitURL;
        var img = new Image();
        img.onload = function () {
            if (this.height != 60) {
                alert("Logo phải có chiều cao đúng 60px");
            }else{
                var reader = new FileReader();
                reader.onload = function (e) {
                    document.getElementById(_data.target).src = e.target.result;
                    // HTTP
                    var http = new XMLHttpRequest();
                    http.onreadystatechange = function (event) {
                        if (http.readyState == 4 && http.status == 200) {
                            need_to_save = true;
                            var server = JSON.parse(http.responseText);
                            if (server.Data.status == "OK") {
                                var oldname = $("#HoldRootContent").contents().find('#' + _data.target)[0].src;
                                oldname = oldname.substring(oldname.lastIndexOf('/') + 1);
                                if (deleted_files != '') {
                                    deleted_files += ';';
                                }
                                deleted_files += oldname;
                                $("#HoldRootContent").contents().find('#' + _data.target)[0].src = '{template_path}/' + server.Data.filename;
                            } else {
                                alert("ERROR: " + ms.Data.message);
                            }
                        }
                    }
                    var data = new FormData();
                    data.append('folder', builderfolder);
                    data.append('version', "version_1");
                    data.append('file', file);
                    http.open('POST', main_domain + "/template/UploadFile", true);
                    http.send(data);            
                }
                reader.readAsDataURL(file);
            }
        };
        img.src = _URL.createObjectURL(file);
    };
    upload_file.click();
}

// Upload thêm hình ảnh vô slide
function UploadNewImageToSlide(_data) {
    var image_id = _data.target1 + (_data.mIndex + 1);
    var control_id = _data.target2 + (_data.mIndex + 1);
    var container_id = _data.container;

    if (upload_file == null) {
        upload_file = document.createElement('input');
        upload_file.type = "file";
        upload_file.style.position = "absolute";
        upload_file.style.zIndex = "-1";
        upload_file.style.width = "0px";
        upload_file.style.height = "0px";
        upload_file.style.opacity = "0";
        document.body.appendChild(upload_file);
    }
    $(upload_file).off('change');
    upload_file.onchange = function () {
        var file = upload_file.files[0];
        var _URL = window.URL || window.webkitURL;
        var img = new Image();
        img.onload = function () {
            if (this.width != 600 || this.height != 800) {
                alert("Hình ảnh phải có kích thước 600x800 (px)");
            } else {
                var reader = new FileReader();
                reader.onload = function (e) {
                    // NEW VIEW
                    var n_img = document.createElement('img');
                    n_img.setAttribute('class', 'slideimage');
                    n_img.setAttribute('id', image_id);
                    n_img.src = e.target.result;
                    $('#slidecontroller').append('<span id="' + control_id + '" onclick="GoSlide(' + (_data.mIndex + 1) + ')"></span>');
                    document.getElementById(container_id).appendChild(n_img);
                    mIndex++;
                    GoSlide(mIndex);
                    // HTTP
                    var http = new XMLHttpRequest();
                    http.onreadystatechange = function (event) {
                        if (http.readyState == 4 && http.status == 200) {
                            need_to_save = true;
                            var server = JSON.parse(http.responseText);
                            if (server.Data.status == "OK") {
                                $($("#HoldRootContent").contents().find('#' + container_id)[0]).append('<img id=' + image_id + ' class="slideimage" src="{template_path}/' + server.Data.filename + '" />');
                            } else {
                                alert("ERROR: " + ms.Data.message);
                            }
                        }
                    }
                    var data = new FormData();
                    data.append('folder', builderfolder);
                    data.append('version', "version_1");
                    data.append('file', file);
                    http.open('POST', main_domain + "/template/UploadFile", true);
                    http.send(data);
                }
                reader.readAsDataURL(file);
            }
        };
        img.src = _URL.createObjectURL(file);
    };
    upload_file.click();
}

// Xóa ảnh đang có trong slide
function RemoveImageFromSlide(_data) {
    var image_id = _data.target1 + _data.cIndex;
    var control_id = _data.target2 + _data.cIndex;

    // Nhớ tên củ
    var oldname = $("#HoldRootContent").contents().find('#' + image_id)[0].src;
    oldname = oldname.substring(oldname.lastIndexOf('/') + 1);
    if (deleted_files != '') {
        deleted_files += ';';
    }
    deleted_files += oldname;

    // Remove view
    $('#' + image_id).remove();
    $('#' + control_id).remove();
    $($("#HoldRootContent").contents().find('#' + image_id)[0]).remove();
    mIndex--;    
    for (var i = _data.cIndex + 1 ; i <= _data.mIndex; i++) {
        $('#' + _data.target1 + i).attr('id', _data.target1 + (i - 1));
        $('#' + _data.target2 + i).click(function () { GoSlide(i - 1); });
        $('#' + _data.target2 + i).attr('id', _data.target2 + (i - 1));
        $($("#HoldRootContent").contents().find('#' +  _data.target1 + i)[0]).attr('id', _data.target1 + (i - 1));
    }
    need_to_save = true;
    GoSlide(1);
}

// Chuyển đổi RGB thành HEX
function rgb2hex(rgb) {
    try {
        if (/^#[0-9A-F]{6}$/i.test(rgb)) return rgb;
        rgb = rgb.match(/^rgb\((\d+),\s*(\d+),\s*(\d+)\)$/);
        function hex(x) {
            return ("0" + parseInt(x).toString(16)).slice(-2);
        }
        return "#" + hex(rgb[1]) + hex(rgb[2]) + hex(rgb[3]);
    } catch (e) {
        return "";
    }
}

// LƯU THAY ĐỔI
function SAVECHANGE(template_name, locations) {
    var form = new FormData();
    var content = document.getElementById('HoldRootContent').contentWindow.document.head.innerHTML+
        document.getElementById('HoldRootContent').contentWindow.document.body.innerHTML;
    form.append('template_folder', builderfolder);    
    form.append('content', content);
    form.append('template_id', template_id);
    form.append('deleted', deleted_files);
    form.append('name', template_name);
    form.append('locations', locations);
    document.getElementById('for-process-loading').style.display = 'block';
    document.getElementById('for-process-loading-img').style.display = 'block';
    var xhr = new XMLHttpRequest();
    xhr.open("POST", main_domain+"/template/UpdateContent", true);
    xhr.timeout = 30000;
    xhr.ontimeout = function () {
        document.getElementById('for-process-loading').style.display = 'none';
        document.getElementById('for-process-loading-img').style.display = 'none';        
    }
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4 && xhr.status == 200) {            
            document.getElementById('for-process-loading').style.display = 'none';
            document.getElementById('for-process-loading-img').style.display = 'none';
            var ms = JSON.parse(xhr.responseText);
            if (ms.Data == "OK") {
                need_to_save = false;
                alert("Đã lưu thành công");
                window.location.reload();
            } else {
                alert(ms.Data);
            }
        }
    }
    xhr.send(form);
}