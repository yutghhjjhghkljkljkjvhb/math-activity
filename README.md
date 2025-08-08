<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>لعبة الكسر</title>
<style>
  /* الأنماط الأساسية */
  body {
    font-family: 'Arial', sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    min-height: 100vh;
    margin: 0;
    background-color: #f0f0f0;
    direction: rtl;
  }
  .game-container {
    text-align: center;
    background-color: #fff;
    padding: 30px;
    border-radius: 15px;
    box-shadow: 0 8px 16px rgba(0,0,0,0.2);
    width: 90%;
    max-width: 600px;
  }
  .rectangle-container {
    display: flex;
    flex-direction: column; /* ترتيب المستطيلات بشكل عمودي */
    align-items: center;
    width: 100%;
    margin-bottom: 30px;
  }
  /* النمط الجديد لكل مستطيل فردي داخل الحاوية */
  .rectangle-wrapper {
    display: flex;
    width: 100%;
    height: 100px;
    border: 4px solid black;
    margin-bottom: 10px; /* مسافة بين المستطيلات */
    overflow: hidden;
  }
  .part {
    flex-grow: 1;
    height: 100%;
    border: 2px solid black;
    background-color: #ffffff;
    cursor: pointer;
    transition: background-color 0.2s ease;
  }
  .part.shaded {
    background-color: #388e3c;
  }
  .info-container {
    margin-bottom: 20px;
  }
  #question {
    font-size: 1.5em;
    margin-bottom: 15px;
    color: #333;
  }
  .fraction-display {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-size: 4em;
    font-weight: bold;
    line-height: 1;
    margin: 20px 0;
  }
  .fraction-display .line {
    width: 100px;
    height: 4px;
    background-color: black;
    margin: 5px 0;
  }
  .buttons-container {
    display: flex;
    justify-content: center;
    gap: 15px;
    margin-top: 20px;
  }
  button {
    padding: 12px 25px;
    font-size: 1.2em;
    font-weight: bold;
    color: #fff;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  #check-btn {
    background-color: #4CAF50;
  }
  #check-btn:hover {
    background-color: #45a049;
  }
  #next-btn {
    background-color: #008CBA;
    display: none;
  }
  #next-btn:hover {
    background-color: #007b9a;
  }
  #result {
    font-size: 1.8em;
    margin-top: 20px;
    font-weight: bold;
  }
  .correct {
    color: #2e7d32;
  }
  .incorrect {
    color: #c62828;
  }
</style>
</head>
<body>

<div class="game-container">
  <div class="info-container">
    <p id="question">قم بتلوين الأجزاء لتساوي الكسر التالي:</p>
    <div class="fraction-display">
      <span id="numerator-display">?</span>
      <div class="line"></div>
      <span id="denominator-display">2</span>
    </div>
  </div>

  <div class="rectangle-container"></div>

  <div class="buttons-container">
    <button id="check-btn">تحقق</button>
    <button id="next-btn">التالي</button>
  </div>

  <div id="result"></div>
</div>

<script>
  const numeratorDisplay = document.getElementById('numerator-display');
  const checkBtn = document.getElementById('check-btn');
  const nextBtn = document.getElementById('next-btn');
  const resultDiv = document.getElementById('result');
  const rectangleContainer = document.querySelector('.rectangle-container');
  const denominator = 2; // المقام ثابت
  let correctNumerator = 0; // البسط متغير

  // دالة مساعدة لإنشاء أجزاء المستطيل
  function createRectangleParts(container) {
    const rectangleWrapper = document.createElement('div');
    rectangleWrapper.classList.add('rectangle-wrapper');
    for (let i = 0; i < denominator; i++) {
        const part = document.createElement('div');
        part.classList.add('part');
        rectangleWrapper.appendChild(part);
    }
    container.appendChild(rectangleWrapper);
  }

  function newGameRound() {
    nextBtn.style.display = 'none';
    checkBtn.style.display = 'block';
    resultDiv.textContent = '';
    
    rectangleContainer.innerHTML = ''; // إفراغ الحاوية من المستطيلات السابقة
    
    // توليد بسط عشوائي بين 1 و 19 (شامل)
    correctNumerator = Math.floor(Math.random() * 19) + 1;
    numeratorDisplay.textContent = correctNumerator;
    
    // حساب عدد المستطيلات الكاملة المطلوبة
    const numFullRectangles = Math.floor((correctNumerator - 1) / denominator) + 1;
    
    // إنشاء العدد الصحيح من المستطيلات
    for (let i = 0; i < numFullRectangles; i++) {
        createRectangleParts(rectangleContainer);
    }
    
    // إعادة ربط أحداث النقر بالأجزاء الجديدة
    const parts = document.querySelectorAll('.part');
    parts.forEach(part => {
      part.addEventListener('click', () => {
        part.classList.toggle('shaded');
      });
    });
  }

  checkBtn.addEventListener('click', () => {
    const shadedCount = document.querySelectorAll('.part.shaded').length;
    
    if (shadedCount === correctNumerator) {
      resultDiv.textContent = 'إجابة صحيحة! أحسنت.';
      resultDiv.className = 'correct';
      nextBtn.style.display = 'block';
      checkBtn.style.display = 'none';
      const parts = document.querySelectorAll('.part');
      parts.forEach(part => part.style.pointerEvents = 'none');
    } else {
      resultDiv.textContent = `إجابة خاطئة. لقد قمت بتلوين ${shadedCount} من أصل ${correctNumerator} أجزاء.`;
      resultDiv.className = 'incorrect';
    }
  });

  nextBtn.addEventListener('click', () => {
    newGameRound();
  });

  // بدء اللعبة لأول مرة
  newGameRound();
</script>

</body>
</html>
