<!doctype html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jadwal Sholat</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: left;
            margin: 0;
            padding: 0;
        }
        table {
            margin: auto;
            border-collapse: collapse;
            width: 70%;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: center;
            white-space: nowrap;
        }
        th {
            background-color: #f2f2f2;
        }
        tr.imsak-row, tr.maghrib-row {
            font-weight: bold;
        }
        .imsak-row td { background-color: lightgreen; }
        .maghrib-row td { background-color: lightblue; }
        .sisa-waktu {
            width: 200px;
        }
        .catatan {
            color: red;
            font-size: 14px;
            text-align: center;
            margin-top: 10px;
        }
        #date-time {
            text-align: center;
            display: block;
            margin: 0 auto;
            font-size: 16px;
            margin-bottom: 20px;
        }
        tr.next-prayer td {
            background-color: yellow !important;
        }
    </style>
</head>
<body>
    <h1></h1>
    <p id="date-time"></p>
    <table>
        <tr>
            <th>Waktu</th>
            <th>Jam (WITA)</th>
            <th class="sisa-waktu">Sisa Waktu</th>
        </tr>
        <tr class="imsak-row"><td>Imsak</td><td id="imsak"></td><td id="sisa-imsak"></td></tr>
        <tr><td>Subuh</td><td id="subuh"></td><td id="sisa-subuh"></td></tr>
        <tr><td>Dzuhur</td><td id="dzuhur"></td><td id="sisa-dzuhur"></td></tr>
        <tr><td>Ashar</td><td id="ashar"></td><td id="sisa-ashar"></td></tr>
        <tr class="maghrib-row"><td>Maghrib</td><td id="maghrib"></td><td id="sisa-maghrib"></td></tr>
        <tr><td>Isya</td><td id="isya"></td><td id="sisa-isya"></td></tr>
    </table>
    <p class="catatan">Berdasarkan data Bimas Islam Kemenag RI</p>
    <audio id="notif-sound" src="https://www.soundjay.com/button/beep-07.wav"></audio>

    <script>
        const jadwalImsakiyah = {
            "2025-03-01": { imsak: "04:54", subuh: "05:04", dzuhur: "12:26", ashar: "15:38", maghrib: "18:29", isya: "19:37" },
            "2025-03-02": { imsak: "04:54", subuh: "05:04", dzuhur: "12:26", ashar: "15:37", maghrib: "18:29", isya: "19:37" },
            "2025-03-03": { imsak: "04:54", subuh: "05:04", dzuhur: "12:25", ashar: "15:36", maghrib: "18:28", isya: "19:37" },
            "2025-03-04": { imsak: "04:54", subuh: "05:04", dzuhur: "12:25", ashar: "15:35", maghrib: "18:28", isya: "19:37" },
            "2025-03-05": { imsak: "04:54", subuh: "05:04", dzuhur: "12:25", ashar: "15:35", maghrib: "18:28", isya: "19:36" },
            "2025-03-06": { imsak: "04:54", subuh: "05:04", dzuhur: "12:25", ashar: "15:34", maghrib: "18:28", isya: "19:36" },
            "2025-03-07": { imsak: "04:53", subuh: "05:03", dzuhur: "12:25", ashar: "15:33", maghrib: "18:27", isya: "19:36" },
            "2025-03-08": { imsak: "04:53", subuh: "05:03", dzuhur: "12:24", ashar: "15:32", maghrib: "18:27", isya: "19:35" },
            "2025-03-09": { imsak: "04:53", subuh: "05:03", dzuhur: "12:24", ashar: "15:31", maghrib: "18:27", isya: "19:35" },
            "2025-03-10": { imsak: "04:53", subuh: "05:03", dzuhur: "12:24", ashar: "15:30", maghrib: "18:27", isya: "19:35" },
            "2025-03-11": { imsak: "04:52", subuh: "05:02", dzuhur: "12:24", ashar: "15:29", maghrib: "18:26", isya: "19:35" },
            "2025-03-12": { imsak: "04:52", subuh: "05:02", dzuhur: "12:23", ashar: "15:29", maghrib: "18:26", isya: "19:34" },
            "2025-03-13": { imsak: "04:52", subuh: "05:02", dzuhur: "12:23", ashar: "15:28", maghrib: "18:26", isya: "19:34" },
            "2025-03-14": { imsak: "04:52", subuh: "05:02", dzuhur: "12:23", ashar: "15:27", maghrib: "18:26", isya: "19:34" },
            "2025-03-15": { imsak: "04:52", subuh: "05:02", dzuhur: "12:22", ashar: "15:26", maghrib: "18:25", isya: "19:33" },
            "2025-03-16": { imsak: "04:51", subuh: "05:01", dzuhur: "12:22", ashar: "15:25", maghrib: "18:25", isya: "19:33" },
            "2025-03-17": { imsak: "04:51", subuh: "05:01", dzuhur: "12:22", ashar: "15:24", maghrib: "18:25", isya: "19:33" },
            "2025-03-18": { imsak: "04:51", subuh: "05:01", dzuhur: "12:22", ashar: "15:23", maghrib: "18:25", isya: "19:33" },
            "2025-03-19": { imsak: "04:50", subuh: "05:00", dzuhur: "12:21", ashar: "15:22", maghrib: "18:24", isya: "19:32" },
            "2025-03-20": { imsak: "04:50", subuh: "05:00", dzuhur: "12:21", ashar: "15:21", maghrib: "18:24", isya: "19:32" },
            "2025-03-21": { imsak: "04:50", subuh: "05:00", dzuhur: "12:21", ashar: "15:20", maghrib: "18:24", isya: "19:32" },
            "2025-03-22": { imsak: "04:50", subuh: "05:00", dzuhur: "12:21", ashar: "15:21", maghrib: "18:24", isya: "19:32" },
            "2025-03-23": { imsak: "04:49", subuh: "04:59", dzuhur: "12:20", ashar: "15:21", maghrib: "18:23", isya: "19:31" },
            "2025-03-24": { imsak: "04:49", subuh: "04:59", dzuhur: "12:20", ashar: "15:21", maghrib: "18:23", isya: "19:31" },
            "2025-03-25": { imsak: "04:49", subuh: "04:59", dzuhur: "12:20", ashar: "15:22", maghrib: "18:22", isya: "19:30" },
            "2025-03-26": { imsak: "04:48", subuh: "04:58", dzuhur: "12:19", ashar: "15:22", maghrib: "18:22", isya: "19:30" },
            "2025-03-27": { imsak: "04:48", subuh: "04:58", dzuhur: "12:19", ashar: "15:23", maghrib: "18:22", isya: "19:30" },
            "2025-03-28": { imsak: "04:48", subuh: "04:58", dzuhur: "12:19", ashar: "15:23", maghrib: "18:22", isya: "19:30" },
            "2025-03-29": { imsak: "04:47", subuh: "04:57", dzuhur: "12:18", ashar: "15:23", maghrib: "18:21", isya: "19:29" },
            "2025-03-30": { imsak: "04:47", subuh: "04:57", dzuhur: "12:18", ashar: "15:24", maghrib: "18:21", isya: "19:29" }
        };

        let lastNotified = {};

        function updateDateTime() {
            const now = new Date();
            const options = { year: 'numeric', month: 'long', day: 'numeric', weekday: 'long' };
            const dateStr = now.toLocaleDateString('id-ID', options);
            const timeStr = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
            document.getElementById("date-time").textContent = `${dateStr}, Jam ${timeStr}`;
        }

        function updateJadwal() {
            const today = new Date();
            const tanggalHariIni = today.toISOString().split('T')[0];

            if (jadwalImsakiyah[tanggalHariIni]) {
                const jadwal = jadwalImsakiyah[tanggalHariIni];

                document.getElementById("imsak").textContent = jadwal.imsak;
                document.getElementById("subuh").textContent = jadwal.subuh;
                document.getElementById("dzuhur").textContent = jadwal.dzuhur;
                document.getElementById("ashar").textContent = jadwal.ashar;
                document.getElementById("maghrib").textContent = jadwal.maghrib;
                document.getElementById("isya").textContent = jadwal.isya;

                updateCountdown(jadwal);
            } else {
                alert("Jadwal untuk hari ini belum tersedia.");
            }
        }

        function updateCountdown(jadwal) {
            const now = new Date();
            let nextPrayer = null;
            let smallestDiff = Infinity;

            const prayers = [
                { name: 'Imsak', time: jadwal.imsak },
                { name: 'Subuh', time: jadwal.subuh },
                { name: 'Dzuhur', time: jadwal.dzuhur },
                { name: 'Ashar', time: jadwal.ashar },
                { name: 'Maghrib', time: jadwal.maghrib },
                { name: 'Isya', time: jadwal.isya }
            ];

            prayers.forEach(prayer => {
                const [hours, minutes] = prayer.time.split(":").map(Number);
                const target = new Date(now);
                target.setHours(hours, minutes, 0, 0);

                if (target < now) target.setDate(target.getDate() + 1);
                const diff = target - now;

                if (diff > 0 && diff < smallestDiff) {
                    smallestDiff = diff;
                    nextPrayer = prayer.name;
                }
            });

            document.querySelectorAll('tr').forEach(row => {
                row.classList.remove('next-prayer');
            });

            if (nextPrayer) {
                document.querySelectorAll('tr').forEach(row => {
                    const td = row.querySelector('td');
                    if (td && td.textContent === nextPrayer) {
                        row.classList.add('next-prayer');
                    }
                });
            }

            function timeRemaining(targetTime, waktu) {
                const [hours, minutes] = targetTime.split(":").map(Number);
                const target = new Date(now);
                target.setHours(hours, minutes, 0, 0);

                if (target < now) target.setDate(target.getDate() + 1);
                const diff = Math.floor((target - now) / 1000);

                if (diff <= 0 && lastNotified[waktu] !== targetTime) {
                    showNotification(`Waktunya ${waktu} sekarang!`);
                    lastNotified[waktu] = targetTime;
                }

                return diff <= 0 ? "Waktunya sekarang!" : `${Math.floor(diff / 3600)}j ${Math.floor((diff % 3600) / 60)}m ${diff % 60}d`;
            }

            document.getElementById("sisa-imsak").textContent = timeRemaining(jadwal.imsak, "Imsak");
            document.getElementById("sisa-subuh").textContent = timeRemaining(jadwal.subuh, "Subuh");
            document.getElementById("sisa-dzuhur").textContent = timeRemaining(jadwal.dzuhur, "Dzuhur");
            document.getElementById("sisa-ashar").textContent = timeRemaining(jadwal.ashar, "Ashar");
            document.getElementById("sisa-maghrib").textContent = timeRemaining(jadwal.maghrib, "Maghrib");
            document.getElementById("sisa-isya").textContent = timeRemaining(jadwal.isya, "Isya");

            setTimeout(() => {
                updateCountdown(jadwal);
                updateDateTime();
            }, 1000);
        }

        function showNotification(message) {
            if ("Notification" in window) {
                if (Notification.permission === "granted") {
                    new Notification(message);
                    playSound();
                } else if (Notification.permission !== "denied") {
                    Notification.requestPermission().then(permission => {
                        if (permission === "granted") {
                            new Notification(message);
                            playSound();
                        }
                    });
                }
            }
        }

        function playSound() {
            document.getElementById("notif-sound").play();
        }

        updateDateTime();
        updateJadwal();
    </script>
</body>
</html>
