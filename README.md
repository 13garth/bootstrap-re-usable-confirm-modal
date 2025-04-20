# Bootstrap re-usable confirm modal
A single template bootstrap confirm modal to reduce code.

```
<div class="modal fade" tabindex="-1" id="form-confirm-modal">
    <div class="modal-dialog">
        <div class="modal-content rounded-0">
            <div class="modal-header">
                <h5 class="modal-title">Important!</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <form id="submit-form-confirm">
                @csrf

                <div class="modal-body">
                    <input type="hidden" id="confirm-route" readonly>
                    <h2 id="confirm-message"></h2>
                </div>
                <div class="modal-footer">
                    <button type="button" class="rounded-0 btn btn-{{ $theme ?? 'light' }}" data-bs-dismiss="modal">
                        <i class="fa fa-times"></i> Cancel
                    </button>
                    <button type="submit" class="rounded-0 btn btn-danger px-5" onclick="form_confirm_action(event)">
                        <i class="fa fa-check"></i> Confirm
                    </button>
                </div>
            </form>
        </div>
    </div>
</div>
<script>
    // NOTE: To use this form, you must have a form with the id 'submit-form-confirm' and a hidden input field with the id 'confirm-id' and 'confirm-route'
    // onclick="confirm_init_modal_data_deletion('{{ route('route.name') }}');"
    // function confirm_init_modal_unique_function_name(id, route) {
    //     $('#form-confirm-modal').modal('show');
    //     $('#confirm-route').val(route);
    //     $('#confirm-message').text('Are you sure you want to perform this action?');
    // }

    function form_confirm_action(event) {
        event.preventDefault();
        $('#preloader').show();

        var url = $('#confirm-route').val();
        $.ajax({
            url: url,
            method: "POST",
            data: $('#submit-form-confirm').serialize(),
            success: function(response) {
                $('#preloader').hide();
                $('#form-confirm-modal').modal('hide');
                if (response.success) {
                    $.toast({
                        heading: "Success!",
                        text: response.message ?? "Success!",
                        icon: "success",
                        showHideTransition: "fade",
                        allowToastClose: false,
                        hideAfter: 1025,
                        stack: false,
                        position: "top-center",
                        textAlign: "left",
                        loader: true
                    });
                    $('#form-confirm-modal').modal('hide');
                    setTimeout(function() {
                        location.reload();
                    }, 1025);
                }
            },
            error: function(jqXHR, textStatus, errorThrown) {
                $('#preloader').hide();
                // Parse the JSON response
                var response = JSON.parse(jqXHR.responseText);
                var errors = response.errors;

                // Extract the error messages
                var errorMessage = "";
                for (var key in errors) {
                    if (errors.hasOwnProperty(key)) {
                        var errorMessages = errors[key];
                        errorMessage += errorMessages.join(" ") + "<br>";
                    }
                }
                $.toast({
                    heading: "Error!",
                    text: response.message ?? "Error",
                    icon: "error",
                    showHideTransition: "fade",
                    allowToastClose: true,
                    hideAfter: false,
                    stack: false,
                    position: "top-center",
                    textAlign: "left",
                    loader: true
                });
            }
        });
    }
</script>
```
