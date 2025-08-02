<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Dogarawa New Extension – Skill Acquisition Data Form</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background-color: #f5f9fc;
      color: #333;
      margin: 0;
      padding: 20px;
    }
    .container {
      max-width: 900px;
      margin: auto;
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    }
    h1, h2, h3 {
      text-align: center;
      color: #1a5f7a;
    }
    h1 {
      margin-bottom: 10px;
    }
    .subtitle {
      text-align: center;
      font-size: 1.1em;
      color: #555;
      margin-bottom: 30px;
    }
    .section {
      margin-bottom: 30px;
      padding: 20px;
      border: 1px solid #e0e0e0;
      border-radius: 8px;
      background-color: #fafafa;
    }
    label {
      display: block;
      margin-top: 12px;
      font-weight: 600;
      color: #1a5f7a;
    }
    input, select, textarea {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      border: 1px solid #ccc;
      border-radius: 6px;
      font-size: 16px;
    }
    button {
      padding: 10px 16px;
      background-color: #1a5f7a;
      color: white;
      border: none;
      border-radius: 6px;
      font-size: 16px;
      cursor: pointer;
    }
    button:hover {
      background-color: #134c63;
    }
    .btn-small {
      font-size: 14px;
      padding: 6px 10px;
    }
    .child-form {
      margin-top: 20px;
      padding: 15px;
      background: #f0f7ff;
      border-radius: 8px;
      border: 1px dashed #1a5f7a;
    }
    .ict-questions {
      margin-top: 15px;
      padding: 15px;
      background-color: #e8f5e9;
      border-radius: 8px;
      display: none;
    }
    .alert {
      position: fixed;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      background-color: #28a745;
      color: white;
      padding: 18px 30px;
      border-radius: 8px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.2);
      z-index: 1000;
      opacity: 0;
      transition: opacity 0.5s ease;
      text-align: center;
    }
    .alert.show {
      opacity: 1;
    }
    .admin-panel {
      margin-top: 40px;
      padding: 25px;
      border: 2px solid #1a5f7a;
      border-radius: 10px;
      background: #f8fdff;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: left;
    }
    th {
      background-color: #1a5f7a;
      color: white;
    }
    .hidden {
      display: none;
    }
    .details-row {
      background-color: #f0f8ff;
    }
    .flex {
      display: flex;
      gap: 10px;
    }
    .view-btn {
      background-color: #0d6efd;
      font-size: 14px;
    }
    .export-btn {
      background-color: #28a745;
      margin-top: 15px;
      width: 100%;
    }
    .details-table {
      width: 100%;
      margin-top: 10px;
      border: 1px solid #0d6efd;
      border-radius: 6px;
      overflow: hidden;
    }
    .details-table td {
      padding: 8px;
      background: #e6f3ff;
    }
  </style>
</head>
<body>

<div class="container">
  <h1>Dogarawa New Extension</h1>
  <p class="subtitle">Skill Acquisition Data Form</p>

  <form id="mainForm">

    <!-- SECTION A: Parent/Guardian -->
    <div class="section">
      <h2>Section A: Parent/Guardian Information</h2>

      <label for="parentName">Full Name *</label>
      <input type="text" id="parentName" required />

      <label for="streetName">Street Name *</label>
      <select id="streetName" required>
        <option value="">Select Street</option>
      </select>

      <label for="houseNumber">House Number *</label>
      <select id="houseNumber" required>
        <option value="">Select House Number</option>
      </select>

      <label for="parentAddress">Full Address (Auto-generated)</label>
      <input type="text" id="parentAddress" readonly />

      <label for="parentPhone">Phone Number</label>
      <input type="tel" id="parentPhone" placeholder="e.g., 08012345678" />
    </div>

    <!-- SECTION B: Children -->
    <div class="section">
      <h2>Section B: Youth Information</h2>
      <p>Add one or more youth under this guardian.</p>

      <div id="childrenContainer"></div>

      <button type="button" id="addChildBtn" class="btn-small">+ Add Another Child</button>
    </div>

    <button type="submit">Submit All Data</button>
  </form>

  <!-- Admin Panel -->
  <div class="admin-panel">
    <h3>Admin Access</h3>
    <input type="password" id="adminPass" placeholder="Enter Passkey" />
    <button id="adminBtn">View Data</button>

    <div id="adminView" class="hidden">
      <h4>Youth Summary</h4>
      <table id="summaryTable">
        <thead>
          <tr>
            <th>First Name</th>
            <th>Surname</th>
            <th>Age</th>
            <th>Skill Interest</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody id="summaryBody"></tbody>
      </table>

      <button id="exportBtn" class="export-btn">Export All Data to Excel (.xlsx)</button>
    </div>
  </div>
</div>

<!-- Success Alert -->
<div class="alert" id="alert">
  Thank you! Your data has been successfully submitted.
</div>

<script>
  // ====== STREET & HOUSE DATA (from document) ======
  const streetData = {
    "GALADIMA STREET": [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22],
    "ONI PEDE CLOSE": [1,2,3,4,5,6,7],
    "ALI BABA CLOSE": [1,2,3,4],
    "ALH. BASHIR CLOSE": [1,2,3,4,5,6,7],
    "ALH. ABDULHADI STREET": [1,2,3,4,5,6,7,8,9],
    "MASALLACI STREET": ["1A","1B","2A","2B",3,4,5,6,7,8,9,10,11,12,13,14,15],
    "KWANTARESH A ROAD": [1,2,3],
    "BABA TAOFIQ CLOSE": [1,2,3],
    "A.A. GALI CRESCENT": [1,2,3,4,5,6,7,8],
    "YUSUF AHMED STREET": [1,2,3,4,5,6,7,8,9,10,11,12],
    "SHEIKH JAFAR CLOSE": [1,2,3,4,5,6,7,8,9,10,11,12],
    "ALH. LAWAN STREET": [1,2,3,4,5,6],
    "CAPTAIN STREET": Array.from({length: 32}, (_,i) => i+1),
    "MAPOL STREET": [1],
    "SAIDU MAI KATAKO STREET": Array.from({length: 21}, (_,i) => i+1),
    "LAYIN GIDAN BABANGIDA STREET": [1,2,3,4,5,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21],
    "LAYIN GIDAN AMMASCO STREET": [1,2,3,4,5,6],
    "DOGARAWA/EXPRESS ROAD (YAN GAWAYI)": [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19],
    "ENG. MUHAMMAD USMAN (MAKU) STREET": [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22],
    "LAYIN GIDAN BARR. SAMINU ABUBAKAR": [1,2,3,4,5,6,7,8]
  };

  // Skill Options
  const skills = {
    Male: ["Tailoring", "Barbing", "ICT (AI)", "Arabic Class"],
    Female: ["Kunshi", "Tailoring", "Plaiting of Hair", "Saloon", "Arabic Class"]
  };

  // DOM Elements
  const streetSelect = document.getElementById("streetName");
  const houseSelect = document.getElementById("houseNumber");
  const parentAddress = document.getElementById("parentAddress");
  const childrenContainer = document.getElementById("childrenContainer");
  const addChildBtn = document.getElementById("addChildBtn");
  const mainForm = document.getElementById("mainForm");
  const alertBox = document.getElementById("alert");

  // Admin
  const adminPass = document.getElementById("adminPass");
  const adminBtn = document.getElementById("adminBtn");
  const adminView = document.getElementById("adminView");
  const summaryBody = document.getElementById("summaryBody");
  const exportBtn = document.getElementById("exportBtn");

  // Store submissions
  let submissions = JSON.parse(localStorage.getItem("dogarawaSubmissions")) || [];

  // Populate streets
  Object.keys(streetData).forEach(street => {
    const opt = document.createElement("option");
    opt.value = street;
    opt.textContent = street;
    streetSelect.appendChild(opt);
  });

  // Update house numbers
  streetSelect.addEventListener("change", updateHouseNumbers);
  function updateHouseNumbers() {
    const street = streetSelect.value;
    houseSelect.innerHTML = "<option value=''>Select House Number</option>";
    if (street && streetData[street]) {
      streetData[street].forEach(num => {
        const opt = document.createElement("option");
        opt.value = num;
        opt.textContent = `No. ${num}`;
        houseSelect.appendChild(opt);
      });
    }
    updateAddress();
  }

  // Update full address
  function updateAddress() {
    const street = streetSelect.value;
    const house = houseSelect.value;
    if (street && house) {
      parentAddress.value = `No. ${house}, ${street}, Dogarawa New Extension`;
    }
  }
  houseSelect.addEventListener("change", updateAddress);

  // Create a new child form
  function createChildForm(index) {
    const div = document.createElement("div");
    div.className = "child-form";
    div.innerHTML = `
      <h3>Youth #${index + 1}</h3>

      <label>Gender *</label>
      <select class="childGender" data-index="${index}" required>
        <option value="">Select Gender</option>
        <option value="Male">Male</option>
        <option value="Female">Female</option>
      </select>

      <label>First Name *</label>
      <input type="text" class="childFirstName" required />

      <label>Surname *</label>
      <input type="text" class="childSurname" required />

      <label>Age *</label>
      <input type="number" class="childAge" min="10" max="35" required />

      <label>School Attended</label>
      <input type="text" class="childSchool" />

      <label>Educational Background *</label>
      <select class="childLevel" required>
        <option value="">Select Level</option>
        <option value="Primary">Primary</option>
        <option value="Secondary">Secondary</option>
        <option value="Tertiary">Tertiary</option>
      </select>

      <label>Phone Number (Optional)</label>
      <input type="tel" class="childPhone" placeholder="e.g., 08012345678" />

      <label>Ability *</label>
      <select class="childAbility" required>
        <option value="Able">Able</option>
        <option value="Disability">Disability</option>
      </select>

      <label>Skill Interest *</label>
      <select class="childSkill" data-index="${index}" required>
        <option value="">Select Skill</option>
      </select>

      <label>Other Skill (if not listed)</label>
      <input type="text" class="childOtherSkill" placeholder="Specify other skill" />

      <div class="ict-questions" id="ict-${index}">
        <label>Do you have access to a Laptop?</label>
        <select class="laptop">
          <option value="No">No</option>
          <option value="Yes">Yes</option>
        </select>

        <label>Do you have access to a Smartphone?</label>
        <select class="smartphone">
          <option value="No">No</option>
          <option value="Yes">Yes</option>
        </select>

        <label>Do you have Internet access?</label>
        <select class="internet">
          <option value="No">No</option>
          <option value="Yes">Yes</option>
        </select>
      </div>
    `;

    // Attach event listeners
    const genderSelect = div.querySelector(".childGender");
    const skillSelect = div.querySelector(".childSkill");
    const ictDiv = div.querySelector(".ict-questions");

    genderSelect.addEventListener("change", function () {
      const gender = this.value;
      const skillOptions = skillSelect;
      skillOptions.innerHTML = "<option value=''>Select Skill</option>";
      if (skills[gender]) {
        skills[gender].forEach(skill => {
          const opt = document.createElement("option");
          opt.value = skill;
          opt.textContent = skill;
          skillOptions.appendChild(opt);
        });
      }
      ictDiv.style.display = "none";
    });

    skillSelect.addEventListener("change", function () {
      ictDiv.style.display = this.value === "ICT (AI)" ? "block" : "none";
    });

    return div;
  }

  // Add first child
  addChildBtn.addEventListener("click", () => {
    const index = childrenContainer.children.length;
    const childForm = createChildForm(index);
    childrenContainer.appendChild(childForm);
  });

  // Add initial child
  addChildBtn.click();

  // Form submission
  mainForm.addEventListener("submit", function (e) {
    e.preventDefault();

    const parentName = document.getElementById("parentName").value;
    const parentPhone = document.getElementById("parentPhone").value;
    const address = document.getElementById("parentAddress").value;

    const children = [];
    document.querySelectorAll(".child-form").forEach((form, idx) => {
      const data = {
        firstName: form.querySelector(".childFirstName").value,
        surname: form.querySelector(".childSurname").value,
        age: form.querySelector(".childAge").value,
        school: form.querySelector(".childSchool").value,
        level: form.querySelector(".childLevel").value,
        phone: form.querySelector(".childPhone").value,
        ability: form.querySelector(".childAbility").value,
        gender: form.querySelector(".childGender").value,
        skill: form.querySelector(".childSkill").value,
        otherSkill: form.querySelector(".childOtherSkill").value,
        laptop: form.querySelector(".laptop")?.value || "No",
        smartphone: form.querySelector(".smartphone")?.value || "No",
        internet: form.querySelector(".internet")?.value || "No"
      };
      children.push(data);
    });

    // Save each child as a separate record
    const date = new Date().toISOString().slice(0, 19);
    children.forEach(child => {
      const record = {
        parentName,
        parentAddress: address,
        parentPhone,
        childFirstName: child.firstName,
        childSurname: child.surname,
        age: child.age,
        school: child.school,
        educationLevel: child.level,
        childPhone: child.phone,
        ability: child.ability,
        gender: child.gender,
        skillInterest: child.skill,
        otherSkill: child.otherSkill,
        laptop: child.laptop,
        smartphone: child.smartphone,
        internet: child.internet,
        date
      };
      submissions.push(record);
    });

    // Save to localStorage
    localStorage.setItem("dogarawaSubmissions", JSON.stringify(submissions));

    // Show success alert
    alertBox.textContent = `Thank you, ${parentName}! Your data has been successfully submitted.`;
    alertBox.classList.add("show");
    setTimeout(() => {
      alertBox.classList.remove("show");
    }, 10000);

    // Reset form
    mainForm.reset();
    childrenContainer.innerHTML = "";
    parentAddress.value = "";
    addChildBtn.click(); // Add one blank child
  });

  // Admin Access
  adminBtn.addEventListener("click", () => {
    if (adminPass.value === "exco2025") {
      adminView.classList.remove("hidden");
      summaryBody.innerHTML = "";

      submissions.forEach((record, index) => {
        const tr = document.createElement("tr");

        tr.innerHTML = `
          <td>${record.childFirstName}</td>
          <td>${record.childSurname}</td>
          <td>${record.age}</td>
          <td>${record.skillInterest}</td>
          <td>
            <button class="view-btn" onclick="toggleDetails(${index})">View Details</button>
          </td>
        `;

        summaryBody.appendChild(tr);

        // Inject Details Row (hidden by default)
        const detailsRow = document.createElement("tr");
        detailsRow.className = "details-row hidden";
        detailsRow.id = `details-${index}`;
        detailsRow.innerHTML = `
          <td colspan="5">
            <table class="details-table">
              <tr><td><strong>Parent Name:</strong> ${record.parentName}</td></tr>
              <tr><td><strong>Parent Phone:</strong> ${record.parentPhone || 'N/A'}</td></tr>
              <tr><td><strong>Full Address:</strong> ${record.parentAddress}</td></tr>
              <tr><td><strong>School:</strong> ${record.school || 'N/A'}</td></tr>
              <tr><td><strong>Education Level:</strong> ${record.educationLevel || 'N/A'}</td></tr>
              <tr><td><strong>Child Phone:</strong> ${record.childPhone || 'N/A'}</td></tr>
              <tr><td><strong>Ability:</strong> ${record.ability}</td></tr>
              <tr><td><strong>Gender:</strong> ${record.gender}</td></tr>
              <tr><td><strong>Other Skill:</strong> ${record.otherSkill || 'None'}</td></tr>
              <tr><td><strong>Laptop:</strong> ${record.laptop}</td></tr>
              <tr><td><strong>Smartphone:</strong> ${record.smartphone}</td></tr>
              <tr><td><strong>Internet:</strong> ${record.internet}</td></tr>
              <tr><td><strong>Date Submitted:</strong> ${record.date}</td></tr>
            </table>
          </td>
        `;
        summaryBody.appendChild(detailsRow);
      });
    } else {
      alert("Invalid passkey! Use 'exco2025'");
    }
  });

  // Toggle Details Visibility
  function toggleDetails(index) {
    const detailsRow = document.getElementById(`details-${index}`);
    if (detailsRow.classList.contains("hidden")) {
      detailsRow.classList.remove("hidden");
    } else {
      detailsRow.classList.add("hidden");
    }
  

  // Export to Excel
  exportBtn.addEventListener("click", () => {
    const ws = XLSX.utils.json_to_sheet(submissions);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "All Submissions");
    XLSX.writeFile(wb, "Dogarawa_Skill_Acquisition_Data.xlsx");
  });

  // Make toggleDetails globally available
  window.toggleDetails = toggleDetails;
</script>

</body>
</html>
