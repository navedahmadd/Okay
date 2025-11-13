// A simple array of country codes to populate the <select> element in HTML
const COUNTRIES = [
    { code: 'US', name: 'United States' },
    { code: 'CA', name: 'Canada' },
    { code: 'IN', name: 'India' },
    { code: 'GB', name: 'United Kingdom' }
    // Add more countries here...
];

const resultsDiv = document.getElementById('results');
const countrySelect = document.getElementById('country-select');
const yearInput = document.getElementById('year-input');
const form = document.getElementById('holiday-form');

// Function to fetch and display holidays
async function fetchHolidays(year, countryCode) {
    // 1. Clear previous results
    resultsDiv.innerHTML = 'Loading...'; 

    const apiUrl = `https://date.nager.at/api/v3/PublicHolidays/${year}/${countryCode}`;
    
    try {
        // 2. Fetch the data
        const response = await fetch(apiUrl);
        
        // 3. Handle non-200 responses (e.g., 404 for unsupported country)
        if (!response.ok) {
            throw new Error(`HTTP Error! Status: ${response.status}. Check if Country Code (${countryCode}) and Year (${year}) are valid.`);
        }
        
        // 4. Parse JSON data
        const holidays = await response.json();

        // 5. Display the holidays
        displayHolidays(holidays, countryCode);

    } catch (error) {
        // 6. Display any errors
        resultsDiv.innerHTML = `<p style="color: red;">Error: ${error.message}</p>`;
    }
}

// Function to create and inject the holiday list into the DOM
function displayHolidays(holidays, countryCode) {
    if (holidays.length === 0) {
        resultsDiv.innerHTML = `No public holidays found for ${countryCode} in that year.`;
        return;
    }

    let html = `<h2>Public Holidays for ${countryCode}</h2><ul>`;
    
    holidays.forEach(holiday => {
        // The API returns 'date', 'localName', and 'name'
        html += `<li><strong>${holiday.date}</strong>: ${holiday.localName} (${holiday.name})</li>`;
    });
    
    html += '</ul>';
    resultsDiv.innerHTML = html;
}


// Event Listener for the form submission
form.addEventListener('submit', function(event) {
    event.preventDefault(); // Stop the default form submission
    
    const selectedCountry = countrySelect.value;
    const inputYear = yearInput.value;
    
    if (selectedCountry && inputYear) {
        fetchHolidays(inputYear, selectedCountry);
    } else {
        resultsDiv.innerHTML = 'Please select a country and enter a year.';
    }
});


// Initial setup: Populate the country select box
window.onload = function() {
    COUNTRIES.forEach(country => {
        const option = document.createElement('option');
        option.value = country.code;
        option.textContent = country.name;
        countrySelect.appendChild(option);
    });
    // Set default year to current year
    yearInput.value = new Date().getFullYear(); 
};
