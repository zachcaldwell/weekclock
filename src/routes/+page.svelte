<script lang="ts">
    import "../app.css";
    import { onMount } from "svelte";

    let canvas: HTMLCanvasElement;
    let ctx: CanvasRenderingContext2D;

    const width = 800 * 2;
    const height = 800 * 2;
    const centerX = width / 2;
    const centerY = height / 2;
    const radius = (width / 2) * 0.9;
    const bgColor = "#222";
    const totalSecondsInWeek = 7 * 24 * 3600;

    onMount(() => {
        ctx = canvas.getContext("2d") as CanvasRenderingContext2D;

        canvas.width = width;
        canvas.height = height;

        drawBackground();
        drawBackCircle();
        drawDaysDividers();
        drawHand();
        drawDayLabels();
        drawInnerRing();
        drawTime();
        // drawOuterRing();

        console.log(secondsSinceRecentSunday() / totalSecondsInWeek);
    });

    function drawBackground() {
        ctx.fillStyle = "#272727";
        ctx.fillRect(0, 0, width, height);
    }

    function drawDaysDividers() {
        ctx.lineWidth = 7;
        ctx.strokeStyle = "#444";

        const numberOfParts = 7;
        const startingAngle = -Math.PI / 2; // -90 degrees in radians

        for (let i = 0; i < numberOfParts; i++) {
            let angle = startingAngle + i * ((2 * Math.PI) / numberOfParts);
            let x = centerX + radius * Math.cos(angle);
            let y = centerY + radius * Math.sin(angle);

            ctx.beginPath();
            ctx.moveTo(centerX, centerY);
            ctx.lineTo(x, y);
            ctx.stroke();
        }
    }

    function drawDayLabels() {
        ctx.lineWidth = 7;
        ctx.strokeStyle = "#444";

        const numberOfParts = 7;
        const startingAngle = -Math.PI / 2; // -90 degrees in radians
        const days = ["SAT", "FRI", "THU", "WED", "TUE", "MON", "SUN"];
        ctx.font = "bold 30px Arial"; // Font size and type for the days
        ctx.textAlign = "center";
        ctx.textBaseline = "middle";
        ctx.fillStyle = "#aaa";

        for (let i = 0; i < numberOfParts; i++) {
            let angle = startingAngle + i * ((2 * Math.PI) / numberOfParts);
            let x = centerX + radius * Math.cos(angle);
            let y = centerY + radius * Math.sin(angle);

            // Place letter
            let textAngle = angle + Math.PI / numberOfParts; // midpoint angle
            let textDistance = radius * 0.85; // % from the center
            let textX = centerX + textDistance * Math.cos(textAngle);
            let textY = centerY + textDistance * Math.sin(textAngle);
            ctx.fillText(days[i], textX, textY);
        }
    }

    function drawBackCircle() {
        ctx.shadowColor = "#111";
        ctx.shadowBlur = 50;
        ctx.fillStyle = bgColor;
        ctx.beginPath();
        ctx.arc(width / 2, height / 2, radius, 0, 2 * Math.PI);
        ctx.fill();
        ctx.shadowBlur = 0;
    }

    function drawOuterRing() {
        ctx.beginPath();
        ctx.lineWidth = 10;
        ctx.strokeStyle = "#444";
        ctx.lineCap = "round";
        ctx.lineJoin = "round";
        ctx.arc(width / 2, height / 2, radius, 0, 2 * Math.PI);
        ctx.stroke();
    }

    function drawInnerRing() {
        ctx.shadowColor = "#111";
        ctx.shadowBlur = 30;

        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.fillStyle = bgColor;
        ctx.arc(width / 2, height / 2, radius * 0.7, 0, 2 * Math.PI);
        ctx.fill();
        ctx.shadowBlur = 0;
    }

    function drawHand() {
        ctx.beginPath();
        ctx.lineWidth = 3;
        ctx.strokeStyle = "#f00";
        ctx.moveTo(centerX, centerY);
        const angle = degreesToRadians(-90 - getHandAngle());
        let x = centerX + radius * Math.cos(angle);
        let y = centerY + radius * Math.sin(angle);

        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.lineTo(x, y);
        ctx.stroke();
    }

    function drawTime() {
        // Define the text to be displayed
        const text = currentTimeString();

        ctx.fillStyle = "#aaa";

        // Set font, alignment, and baseline
        ctx.font = "600 90px Arial";
        ctx.textAlign = "center"; // Align text horizontally to the center
        ctx.textBaseline = "middle"; // Align text vertically to the middle

        // Draw the text
        ctx.fillText(text, centerX, centerY);
    }

    function degreesToRadians(degrees: number) {
        return degrees * (Math.PI / 180);
    }

    function secondsSinceRecentSunday() {
        const date = new Date();

        // Assuming days of the week are numbered 0 (Sunday) to 6 (Saturday)
        const dayOfWeek = date.getDay();

        // Calculate seconds passed in the current day
        let secondsToday =
            date.getHours() * 3600 + date.getMinutes() * 60 + date.getSeconds();

        // Calculate total seconds between the start of Sunday and now
        let totalSeconds = dayOfWeek * 24 * 3600 + secondsToday;

        return totalSeconds;
    }

    function getHandAngle() {
        return (360 * secondsSinceRecentSunday()) / totalSecondsInWeek;
    }

    function currentTimeString() {
        const now = new Date();

        let hours = now.getHours();
        const minutes = String(now.getMinutes()).padStart(2, "0");

        // Determine AM or PM suffix
        const amOrPm = hours >= 12 ? "PM" : "AM";

        // Convert hour from 24-hour to 12-hour format
        hours = hours % 12;
        // Display 12 instead of 0 for the noon/midnight hour
        hours = hours ? hours : 12;

        const timeString = `${String(hours).padStart(
            2,
            "0"
        )}:${minutes} ${amOrPm}`;
        return timeString;
    }
</script>

<canvas bind:this={canvas} class="w-[800px] h-[800px] rounded-lg" />
