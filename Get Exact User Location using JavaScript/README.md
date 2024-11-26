https://www.youtube.com/shorts/J_hHYKu6rpo

https://nominatim.openstreetmap.org/ui/search.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Get User Location</title>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
</head>
<body>
    <h1>User Location with Address</h1>
    <form action="#">
        <select class="select-active" name="location" id="location">
            <option value="">Your Location</option>
        </select>
    </form>
    <p id="output"></p>
    <p id="address"></p>

    <script>
        $(document).ready(function () {
            console.log("Document ready");
            getUserLocation();
        });

        function getUserLocation() {
            const output = $('#output');
            const addressOutput = $('#address');

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    function (position) {
                        const latitude = position.coords.latitude;
                        const longitude = position.coords.longitude;
                        output.text(`Latitude: ${latitude}, Longitude: ${longitude}`);
                        console.log(`Latitude: ${latitude}, Longitude: ${longitude}`);

                        // Call OpenStreetMap API for reverse geocoding
                        const url = `https://nominatim.openstreetmap.org/reverse?lat=${latitude}&lon=${longitude}&format=jsonv2`;

                        fetch(url)
                            .then(response => response.json())
                            .then(data => {
                                if (data && data.address) {
                                    const { city, state, country } = data.address;
                                    const locationText = `${city || ''}, ${state || ''}, ${country || ''}`;
                                    addressOutput.text(`Address: ${locationText}`);

                                    // Populate the select dropdown with address
                                    const locationDropdown = $('#location');
                                    locationDropdown.empty();
                                    locationDropdown.append(`<option value="address">${locationText}</option>`);
                                    locationDropdown.val('address'); // Select the newly added option
                                } else {
                                    addressOutput.text("Address details not available.");
                                }
                            })
                            .catch(error => {
                                console.error("Error fetching address:", error);
                                addressOutput.text("Unable to fetch address details.");
                            });
                    },
                    function (error) {
                        switch (error.code) {
                            case error.PERMISSION_DENIED:
                                output.text("User denied the request for Geolocation.");
                                break;
                            case error.POSITION_UNAVAILABLE:
                                output.text("Location information is unavailable.");
                                break;
                            case error.TIMEOUT:
                                output.text("The request to get user location timed out.");
                                break;
                            default:
                                output.text("An unknown error occurred.");
                                break;
                        }
                    }
                );
            } else {
                output.text("Geolocation is not supported by this browser.");
            }
        }
    </script>
</body>
</html>
```