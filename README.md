<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RCIS (Rider Check-In System)</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 20px;
            background-color: #f4f4f4;
            position: relative;
        }
        .logo {
            width: 250px;
            margin-bottom: 20px;
        }
        h1 {
            font-size: 36px;
            color: #333;
            margin-bottom: 30px;
        }
        button {
            display: inline-block;
            padding: 20px 40px;
            font-size: 24px;
            font-weight: bold;
            color: white;
            background-color: #007BFF;
            border: none;
            border-radius: 10px;
            margin: 20px;
            transition: 0.3s;
            box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
            transform: scale(1.1);
        }
        #buttons {
            display: none;
        }
        #access-denied {
            display: none;
            color: red;
            font-size: 20px;
            margin-top: 50px;
        }
        #shift-time-selection {
            display: none;
            margin-top: 20px;
            text-align: center;
        }
        #shift-time-selection select {
            padding: 10px;
            font-size: 16px;
            border-radius: 5px;
            border: 1px solid #ccc;
            margin-left: 10px;
        }
    </style>
    <script>
        function getDistanceFromLatLonInMeters(lat1, lon1, lat2, lon2) {
            const R = 6371000;
            const dLat = (lat2 - lat1) * Math.PI / 180;
            const dLon = (lon2 - lon1) * Math.PI / 180;
            const a =
                Math.sin(dLat / 2) * Math.sin(dLat / 2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
                Math.sin(dLon / 2) * Math.sin(dLon / 2);
            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
            return R * c;
        }

        function checkLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(position => {
                    const userLat = position.coords.latitude;
                    const userLon = position.coords.longitude;
                    const allowedLat = 28.460845;
                    const allowedLon = 77.079439;
                    const distance = getDistanceFromLatLonInMeters(userLat, userLon, allowedLat, allowedLon);

                    if (distance <= 200) {
                        document.getElementById('buttons').style.display = 'block';
                        document.getElementById('access-denied').style.display = 'none';
                    } else {
                        document.getElementById('access-denied').style.display = 'block';
                        document.getElementById('buttons').style.display = 'none';
                    }
                }, error => {
                    console.error("Geolocation error:", error);
                    let errorMessage = 'Geolocation access denied. ';
                    switch (error.code) {
                        case error.PERMISSION_DENIED:
                            errorMessage += "Permission denied. Please allow location access.";
                            break;
                        case error.POSITION_UNAVAILABLE:
                            errorMessage += "Location information is unavailable.";
                            break;
                        case error.TIMEOUT:
                            errorMessage += "The request to get user location timed out.";
                            break;
                        case error.UNKNOWN_ERROR:
                            errorMessage += "An unknown error occurred.";
                            break;
                    }
                    alert(errorMessage + " " + errorMessage + " Please ensure location services are enabled and allow this site to access your location."); //show detailed error
                    document.getElementById('buttons').style.display = 'none';
                    document.getElementById('access-denied').style.display = 'block';
                }, {
                    enableHighAccuracy: true,
                    timeout: 20000,
                    maximumAge: 0
                });
            } else {
                alert('Geolocation is not supported by this browser.');
                document.getElementById('buttons').style.display = 'block';
            }
        }

        function openForm(url) {
            let formWindow = window.open(url, '_blank', 'noopener,noreferrer');
            if (formWindow) {
                let checkClosed = setInterval(function() {
                    if (formWindow && formWindow.closed) {
                        clearInterval(checkClosed);
                        window.location.reload();
                    }
                }, 1000);
            } else {
                alert("Pop-up blocked. Please allow pop-ups for this site.");
            }
        }

        document.addEventListener('contextmenu', event => event.preventDefault());

        function showShiftTimeSelection(action) {
            const shiftTimeSelection = document.getElementById('shift-time-selection');
            const checkInButton = document.getElementById('check-in-button');
            if (action === 'checkin') {
                shiftTimeSelection.style.display = 'block';
                checkInButton.onclick = function() {
                    const shiftTime = document.getElementById('shift-time-select').value;
                    if (shiftTime) {
                        const checkInUrl = `https://docs.google.com/forms/d/e/1FAIpQLSfzDStVTdLCGf2N9mO8H-LaKANcAeE2QR6BuYIadVmVMz0j8w/viewform?usp=sharing&entry.xxxxxxxxxxxx=${shiftTime}`;
                        openForm(checkInUrl);
                    } else {
                        alert('Please select your shift time.');
                    }
                };
            } else {
                shiftTimeSelection.style.display = 'none';
                checkInButton.onclick = function() {
                    openForm('https://docs.google.com/forms/d/e/1FAIpQLScGzx3G6WBI5LzGH_54sPVyg642i_F-ekLPpMaW0zJybCyPtQ/viewform?usp=sharing');
                };
            }
        }
    </script>
</head>
<body onload="checkLocation()">
    <div style="display: flex; flex-direction: column; align-items: center;">

        <h1>RCIS (Rider Check-In System)</h1>

        <div id="buttons">
            <button id="check-in-button" onclick="showShiftTimeSelection('checkin')">  Check-In
            </button>
            <button onclick="openForm('https://docs.google.com/forms/d/e/1FAIpQLScGzx3G6WBI5LzGH_54sPVyg642i_F-ekLPpMaW0zJybCyPtQ/viewform?usp=sharing')">
                Check-Out
            </button>
        </div>

        <div id="shift-time-selection" style="display: none;">  <label for="shift-time-select">Select Shift Time:</label>
            <select id="shift-time-select">
                <option value="">Select Shift Time</option>
                <option value="7 AM to 5 PM">7 AM to 5 PM</option>
                <option value="8 AM to 6 PM">8 AM to 6 PM</option>
                <option value="4 PM to 2 AM">4 PM to 2 AM</option>
                <option value="8 PM to 6 AM">8 PM to 6 AM</option>
                <option value="10 PM to 8 AM">10 PM to 8 AM</option>
                <option value="Other (In Emergency Only)">Other (In Emergency Only)</option>
            </select>
        </div>

        <p id="access-denied">Access denied: You are not within the allowed location range.</p>
    </div>
</body>
</html>
