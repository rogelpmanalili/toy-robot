Simulate Toy Robot 

A simple program to simulate a toy robot moving on a square tabletop, of dimensions 5 units x 5 units. There are no other obstructions on the table surface. The robot is free to roam around the surface of the table, but must be prevented from falling to destruction. Any movement that would result in the robot falling from the table must be prevented, however further valid movement commands must still be allowed. All commands should be discarded until a valid place command has been executed.


How to install and use:
The program is written using plain HTML and Javascript. No installation is required. Copy/download the file"toy_robot.htm" from Github. Open in any web browser and/or use Console (Developer Tool).


Commands:
All commands should provide output indicating whether or not they succeeded.

PLACE X,Y,DIRECTION
X and Y are integers that indicate a location on the tabletop.
DIRECTION is a string indicating which direction the robot should face. It it one of the four cardinal directions: NORTH, EAST, SOUTH or WEST.

MOVE
Instructs the robot to move 1 square in the direction it is facing.

LEFT
Instructs the robot to rotate 90° anticlockwise/counterclockwise.

RIGHT
Instructs the robot to rotate 90° clockwise.

REPORT
Outputs the robot's current location on the tabletop and the direction it is facing.   





Html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Exam System</title>
</head>
<body>
    <h1>Exam System</h1>
    <div id="exam-container"></div>
    <button onclick="submitExam()">Submit Exam</button>

    <script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
    <script src="exam.js"></script>
</body>
</html>


exam.js

$(document).ready(function () {
    // SharePoint API URL
    var apiUrl = "<Your SharePoint API URL>";

    // Function to fetch questions from SharePoint
    function getQuestions() {
        $.ajax({
            url: apiUrl + "/_api/web/lists/getbytitle('Questions')/items",
            method: "GET",
            headers: { "Accept": "application/json; odata=verbose" },
            success: function (data) {
                // Process and display questions
                displayQuestions(data.d.results);
            },
            error: function (error) {
                console.log(JSON.stringify(error));
            }
        });
    }

    // Function to display questions in the exam form
    function displayQuestions(questions) {
        var examContainer = $("#exam-container");

        questions.forEach(function (question) {
            var options = question.Options.split(','); // Assuming options are stored as comma-separated values
            var questionHtml = "<p>" + question.Question + "</p>";

            options.forEach(function (option, index) {
                questionHtml += "<input type='radio' name='q" + question.Id + "' value='" + option + "'>" + option + "<br>";
            });

            examContainer.append(questionHtml);
        });
    }

    // Function to submit the exam
    window.submitExam = function () {
        var userResult = {};

        // Collect user's answers
        $("input[type='radio']:checked").each(function () {
            var questionId = $(this).attr("name").substring(1);
            var answer = $(this).val();
            userResult[questionId] = answer;
        });

        // Calculate and display result
        var pass = calculateResult(userResult);
        alert(pass ? "Congratulations! You passed!" : "Sorry, you failed.");

        // Save result to SharePoint
        saveResult(userResult, pass);

        // Send result via email (you would need a server-side component for this)
        sendEmail(userResult, pass);
    };

    // Function to calculate the result
    function calculateResult(userResult) {
        // Implement your logic to compare user answers with correct answers
        // For simplicity, let's assume at least 50% correct answers for passing
        var correctAnswers = 0;
        var totalQuestions = Object.keys(userResult).length;

        // Assuming correct answers are stored in SharePoint along with questions
        // Adjust this part based on your data structure
        $.ajax({
            url: apiUrl + "/_api/web/lists/getbytitle('Questions')/items",
            method: "GET",
            headers: { "Accept": "application/json; odata=verbose" },
            success: function (data) {
                data.d.results.forEach(function (question) {
                    if (userResult[question.Id] === question.CorrectAnswer) {
                        correctAnswers++;
                    }
                });
            },
            error: function (error) {
                console.log(JSON.stringify(error));
            },
            async: false // Ensure the correctAnswers count is available before proceeding
        });

        return (correctAnswers / totalQuestions) >= 0.5;
    }

    // Function to save the result to SharePoint
    function saveResult(userResult, pass) {
        // Implement logic to update the SharePoint list with user results
        // You may need to add a column "UserResult" to the list for this
        // Adjust this part based on your data structure
        $.each(userResult, function (questionId, answer) {
            var itemId = parseInt(questionId);
            $.ajax({
                url: apiUrl + "/_api/web/lists/getbytitle('Questions')/items(" + itemId + ")",
                method: "MERGE",
                headers: {
                    "Accept": "application/json;odata=verbose",
                    "Content-Type": "application/json;odata=verbose",
                    "X-HTTP-Method": "MERGE",
                    "If-Match": "*"
                },
                data: JSON.stringify({
                    UserResult: pass ? "Pass" : "Fail"
                }),
                success: function (data) {
                    console.log("Result saved successfully");
                },
                error: function (error) {
                    console.log(JSON.stringify(error));
                }
            });
        });
    }

    // Function to send the result via email (you would need a server-side component for this)
    function sendEmail(userResult, pass) {
        // Implement logic to send an email with the result
        // This typically involves a server-side component to handle email sending
        // Adjust this part based on your email sending mechanism
    }

    // Fetch questions when the page is loaded
    getQuestions();
});


language

// Sample JavaScript using SharePoint REST API

// Function to get questions from SharePoint
function getQuestions(languageCode) {
    var apiUrl = _spPageContextInfo.webAbsoluteUrl +
        "/_api/web/lists/getbytitle('Questions')/items?$select=ID,QuestionText,OptionA,OptionB,OptionC,OptionD,CorrectAnswer&$filter=LanguageLookupField eq '" + languageCode + "'";

    return $.ajax({
        url: apiUrl,
        method: "GET",
        headers: {
            "Accept": "application/json; odata=verbose"
        }
    });
}

// Function to display questions on the webpage
function displayQuestions(questions) {
    // Iterate through the questions and build the HTML
    questions.forEach(function (question) {
        var html = "<div>";
        html += "<p>" + question.QuestionText + "</p>";
        html += "<label><input type='radio' name='q" + question.ID + "' value='A'>" + question.OptionA + "</label><br>";
        html += "<label><input type='radio' name='q" + question.ID + "' value='B'>" + question.OptionB + "</label><br>";
        html += "<label><input type='radio' name='q" + question.ID + "' value='C'>" + question.OptionC + "</label><br>";
        html += "<label><input type='radio' name='q" + question.ID + "' value='D'>" + question.OptionD + "</label><br>";
        html += "</div>";

        // Append the HTML to a container element on your page
        $("#questionsContainer").append(html);
    });
}

// Sample usage
var selectedLanguageCode = "en"; // English language code
getQuestions(selectedLanguageCode).done(function (data) {
    var questions = data.d.results;
    displayQuestions(questions);
});




timer

var siteUrl = "Your SharePoint Site URL";
var listName = "Your Questions List Name";

var endpoint = siteUrl + "/_api/web/lists/getbytitle('" + listName + "')/items";

var timer = 900; // 15 minutes in seconds

var countdown = setInterval(function() {
    timer--;
    // Update the timer display
    document.getElementById('timer').innerHTML = formatTime(timer);

    // Check if the time has run out
    if (timer <= 0) {
        clearInterval(countdown);
        // Implement logic for submitting the test or ending the test
    }
}, 1000);

function formatTime(seconds) {
    var minutes = Math.floor(seconds / 60);
    var remainingSeconds = seconds % 60;
    return minutes + ":" + (remainingSeconds < 10 ? "0" : "") + remainingSeconds;
}



