<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <title>เช็คลิสต์กิจวัตรประจำเดือน</title>
    <style>
        body { font-family: sans-serif; max-width: 450px; margin: 30px auto; padding: 20px; background: #121212; color: #fff; }
        h2 { font-size: 1.2rem; color: #a29bfe; }
        .task-item { background: #1e1e1e; padding: 12px; margin-bottom: 10px; border-radius: 8px; display: flex; align-items: center; justify-content: space-between; border: 1px solid #333; }
        .task-item label { font-size: 1rem; cursor: pointer; display: flex; align-items: center; gap: 10px; }
        input[type="checkbox"] { width: 20px; height: 20px; cursor: pointer; }
        .date-info { font-size: 0.9rem; color: #b2bec3; margin-bottom: 20px; }
    </style>
</head>
<body>

    <h2 id="month-title">เช็คลิสต์กิจวัตรประจำเดือน</h2>
    <div class="date-info" id="date-display"></div>

    <div id="task-container">
        <!-- รายการกิจวัตรจะถูกสร้างขึ้นมาตรงนี้อัตโนมัติ -->
    </div>

    <script>
        // รายการหัวข้อที่คุณโกะต้องการ
        const tasks = [
            { id: 'clean', name: 'ทำความสะอาด' },
            { id: 'jp', name: 'เวลาเรียนภาษาญี่ปุ่น' },
            { id: 'en', name: 'เวลาเรียนภาษาอังกฤษ' },
            { id: 'dhamma', name: 'เวลาเรียนธรรมะ' }
        ];

        // ดึงวันที่ปัจจุบัน
        const now = new Date();
        const year = now.getFullYear();
        const month = now.getMonth() + 1; // 1-12
        const day = now.getDate();

        // สร้าง Key ที่ผูกกับ "เดือนปัจจุบัน" (เช่น "tasks_2026-10") 
        // พ่อขึ้นเดือนใหม่ Key จะเปลี่ยน ทำให้ข้อมูลเดือนเก่าหายไปอัตโนมัติ
        const currentMonthKey = `tasks_${year}_${month}`;

        // แสดงหัวข้อเดือนและวันที่ปัจจุบัน
        document.getElementById('month-title').innerText = `กิจวัตรประจำเดือน ${now.toLocaleString('default', { month: 'long', year: 'numeric' })}`;
        document.getElementById('date-display').innerText = `วันนี้วันที่: ${day} / ${month} / ${year}`;

        // โหลดข้อมูลความคืบหน้าที่บันทึกไว้ในเดือนนี้ (ถ้าไม่มีให้เป็น Object ว่าง)
        let savedData = JSON.parse(localStorage.getItem(currentMonthKey)) || {};

        const container = document.getElementById('task-container');

        // สร้าง HTML สำหรับแต่ละหัวข้อ
        tasks.forEach(task => {
            const isChecked = savedData[task.id] || false;

            const div = document.createElement('div');
            div.className = 'task-item';
            div.innerHTML = `
                <label>
                    <input type="checkbox" data-id="${task.id}" ${isChecked ? 'checked' : ''}>
                    ${task.name}
                </label>
                <span>${isChecked ? '✅ เสร็จแล้ว' : '⏳ ยังไม่ทำ'}</span>
            `;
            container.appendChild(div);
        });

        // ดักจับเวลาผู้ใช้กดติ๊กถูก
        container.addEventListener('change', (e) => {
            if (e.target.type === 'checkbox') {
                const taskId = e.target.getAttribute('data-id');
                const isChecked = e.target.checked;

                // อัปเดตสถานะลงในข้อมูลของเดือนนี้
                savedData[taskId] = isChecked;

                // บันทึกลง LocalStorage ทันที
                localStorage.setItem(currentMonthKey, JSON.stringify(savedData));

                // รีเฟรชข้อความสถานะด้านหลัง (เสร็จแล้ว / ยังไม่ทำ) ให้เปลี่ยนตามทันที
                const statusSpan = e.target.closest('.task-item').querySelector('span');
                statusSpan.innerText = isChecked ? '✅ เสร็จแล้ว' : '⏳ ยังไม่ทำ';
            }
        });
    </script>
</body>
</html>
