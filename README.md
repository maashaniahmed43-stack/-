
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حاسبة القروض </title>
    <style>
        :root {
            --primary: #2c3e50;
            --secondary: #3498db;
            --bg: #f4f7f6;
            --card-bg: #ffffff;
            --text: #333;
            --border: #ddd;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
        }

        .container {
            background-color: var(--card-bg);
            width: 100%;
            max-width: 900px;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
        }

        h1 {
            text-align: center;
            color: var(--primary);
            margin-bottom: 10px;
            font-size: 24px;
        }

        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 30px;
            font-size: 14px;
        }

        .form-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
        }

        @media (max-width: 600px) {
            .form-grid {
                grid-template-columns: 1fr;
            }
        }

        .input-group {
            display: flex;
            flex-direction: column;
        }

        .input-group label {
            margin-bottom: 8px;
            font-weight: 600;
            font-size: 14px;
        }

        .input-group input {
            padding: 12px;
            border: 1px solid var(--border);
            border-radius: 8px;
            font-size: 16px;
            font-family: inherit;
            transition: border-color 0.3s;
        }

        .input-group input:focus {
            outline: none;
            border-color: var(--secondary);
        }

        .input-group small {
            color: #888;
            margin-top: 5px;
            font-size: 12px;
        }

        .btn-calculate {
            grid-column: 1 / -1;
            background-color: var(--secondary);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 8px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
            transition: background-color 0.3s;
        }

        .btn-calculate:hover {
            background-color: #2980b9;
        }

        .results {
            margin-top: 30px;
            padding: 20px;
            background-color: #e8f4fd;
            border-radius: 8px;
            border-right: 5px solid var(--secondary);
            display: none;
        }

        .results h2 {
            margin-top: 0;
            font-size: 20px;
            color: var(--primary);
        }

        .result-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
            font-size: 16px;
        }

        .result-item span:last-child {
            font-weight: bold;
            color: var(--primary);
        }

        .table-container {
            margin-top: 30px;
            overflow-x: auto;
            display: none;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 14px;
            text-align: center;
        }

        th, td {
            padding: 10px;
            border: 1px solid var(--border);
        }

        th {
            background-color: var(--primary);
            color: white;
            font-weight: 600;
            position: sticky;
            top: 0;
        }

        tr:nth-child(even) {
            background-color: #f9f9f9;
        }

        tr:hover {
            background-color: #f1f1f1;
        }

        .currency {
            font-weight: bold;
            color: #27ae60;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>حاسبة القروض</h1>
    <p class="subtitle">طريقة الحساب: القسط الثابت مع الفائدة على الرصيد المتناقص</p>

    <div class="form-grid">
        <div class="input-group">
            <label for="principal">المبلغ الأصلي (P)</label>
            <input type="number" id="principal" placeholder="مثال: 20000" min="1" step="any">
        </div>

        <div class="input-group">
            <label for="annualRate">معدل الربح السنوي (%)</label>
            <input type="number" id="annualRate" placeholder="مثال: 5.805" min="0" step="any">
        </div>

        <div class="input-group">
            <label for="years">مدة القرض (بالسنوات)</label>
            <input type="number" id="years" placeholder="مثال: 10" min="1" step="1">
        </div>
        
        <div class="input-group">
            <label for="months">أو مدة القرض (بالأشهر)</label>
            <input type="number" id="months" placeholder="مثال: 120" min="1" step="1">
            <small>سيتم استخدام هذه القيمة إذا تم إدخالها بدلاً من السنوات.</small>
        </div>

        <button class="btn-calculate" onclick="calculateLoan()">احسب القسط والجدول</button>
    </div>

    <div class="results" id="results">
        <h2>ملخص النتائج</h2>
        <div class="result-item">
            <span>القسط الشهري:</span>
            <span id="monthlyPayment" class="currency">0</span>
        </div>
        <div class="result-item">
            <span>إجمالي المبلغ المدفوع:</span>
            <span id="totalPayment" class="currency">0</span>
        </div>
        <div class="result-item">
            <span>إجمالي الفوائد (الربح):</span>
            <span id="totalInterest" class="currency">0</span>
        </div>
        <div class="result-item">
            <span>عدد الدفعات:</span>
            <span id="totalMonths">0</span>
        </div>
    </div>

    <div class="table-container" id="tableContainer">
        <h2 style="text-align: center; margin-bottom: 15px;">جدول السداد التفصيلي</h2>
        <table id="amortizationTable">
            <thead>
                <tr>
                    <th>الدفعة</th>
                    <th>القسط الشهري</th>
                    <th>من الأصل</th>
                    <th>الربح (الفائدة)</th>
                    <th>الرصيد المتبقي</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <!-- سيتم تعبئة الجدول هنا بواسطة JavaScript -->
            </tbody>
        </table>
    </div>
</div>

<script>
    function calculateLoan() {
        // 1. جلب القيم من الحقول
        const principal = parseFloat(document.getElementById('principal').value);
        const annualRate = parseFloat(document.getElementById('annualRate').value);
        const yearsInput = document.getElementById('years').value;
        const monthsInput = document.getElementById('months').value;

        // 2. التحقق من صحة البيانات
        if (isNaN(principal) || principal <= 0) {
            alert('الرجاء إدخال مبلغ أصلي صحيح (أكبر من صفر).');
            return;
        }
        if (isNaN(annualRate) || annualRate < 0) {
            alert('الرجاء إدخال معدل ربح سنوي صحيح.');
            return;
        }

        let n = 0;
        if (monthsInput && !isNaN(parseInt(monthsInput)) && parseInt(monthsInput) > 0) {
            n = parseInt(monthsInput);
        } else if (yearsInput && !isNaN(parseInt(yearsInput)) && parseInt(yearsInput) > 0) {
            n = parseInt(yearsInput) * 12;
        } else {
            alert('الرجاء إدخال مدة القرض بالسنوات أو بالأشهر.');
            return;
        }

        // 3. حساب معدل الربح الشهري (r)
        const r = (annualRate / 100) / 12;

        // 4. تطبيق الصيغة: P * r / (1 - (1 + r)^-n)
        let monthlyPayment = 0;
        
        if (r === 0) {
            // في حالة عدم وجود فوائد
            monthlyPayment = principal / n;
        } else {
            monthlyPayment = (principal * r) / (1 - Math.pow(1 + r, -n));
        }

        // 5. حساب الإجماليات
        const totalPayment = monthlyPayment * n;
        const totalInterest = totalPayment - principal;

        // 6. عرض النتائج في الملخص
        document.getElementById('monthlyPayment').textContent = monthlyPayment.toFixed(2);
        document.getElementById('totalPayment').textContent = totalPayment.toFixed(2);
        document.getElementById('totalInterest').textContent = totalInterest.toFixed(2);
        document.getElementById('totalMonths').textContent = n;
        
        document.getElementById('results').style.display = 'block';

        // 7. إنشاء جدول السداد التفصيلي (Amortization Schedule)
        generateAmortizationTable(principal, r, monthlyPayment, n);
    }

    function generateAmortizationTable(principal, r, monthlyPayment, n) {
        const tbody = document.getElementById('tableBody');
        tbody.innerHTML = ''; // مسح الجدول القديم

        let balance = principal;

        for (let i = 1; i <= n; i++) {
            // حساب الفائدة لهذا الشهر
            const interestPayment = balance * r;
            
            // حساب المبلغ المدفوع من الأصل
            let principalPayment = monthlyPayment - interestPayment;
            
            // في الدفعة الأخيرة، قد يكون هناك فرق بسيط بسبب التقريب، نقوم بضبطه
            if (i === n) {
                principalPayment = balance;
            }

            // حساب الرصيد المتبقي
            balance = balance - principalPayment;

            // إنشاء صف في الجدول
            const row = document.createElement('tr');
            
            // تنسيق الأرقام
            const formattedMonthly = monthlyPayment.toFixed(2);
            const formattedPrincipal = principalPayment.toFixed(2);
            const formattedInterest = interestPayment.toFixed(2);
            const formattedBalance = Math.abs(balance) < 0.01 ? "0.00" : balance.toFixed(2);

            row.innerHTML = `
                <td>${i}</td>
                <td>${formattedMonthly}</td>
                <td>${formattedPrincipal}</td>
                <td>${formattedInterest}</td>
                <td>${formattedBalance}</td>
            `;
            
            tbody.appendChild(row);
        }

        // إظهار الجدول
        document.getElementById('tableContainer').style.display = 'block';
    }
</script>

</body>
</html>
