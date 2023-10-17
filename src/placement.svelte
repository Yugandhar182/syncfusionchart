<script>
    import { onMount,afterUpdate  } from 'svelte';
    import { Chart, StackingColumnSeries, Category, DataLabel, Tooltip, Legend,Highlight } from '@syncfusion/ej2-charts';
    import { Browser } from '@syncfusion/ej2-base';

    Chart.Inject(StackingColumnSeries, Category, DataLabel, Tooltip, Legend,Highlight);

    let chartData = []; // Initialize with an empty array
    import { dateStore } from './DateStore.js'; // Import the store

  let startDate = '';
  let endDate = '';

  // Function to check if local storage has date information
  function getDatesFromLocalStorage() {
    const storedStartDate = localStorage.getItem('startDate');
    const storedEndDate = localStorage.getItem('endDate');

    if (storedStartDate && storedEndDate) {
      startDate = storedStartDate;
      endDate = storedEndDate;
    }
  }

  // Subscribe to the dateStore
  dateStore.subscribe((value) => {
    startDate = value.startDate;
    endDate = value.endDate;
    fetchData(startDate, endDate); // Fetch data whenever the date changes
  });

   
  async function fetchData(startDate, endDate) {
        try {
            const apiUrl = "https://api.recruitly.io/api/dashboard/ats/data/placementmonthlyusermetrics";
            const apiKey = "TEST45684CB2A93F41FC40869DC739BD4D126D77";
            const response = await fetch(`${apiUrl}?start=${startDate}&end=${endDate}&apiKey=${apiKey}`);

            if (response.ok) {
                const jsonData = await response.json();
                console.log(jsonData);

                // Create an array of unique month names dynamically
                const monthNames = [...new Set(jsonData.map(item => item.monthLabel))];
                monthNames.sort((a, b) => {
                    const [aMonth, aYear] = a.split('/');
                    const [bMonth, bYear] = b.split('/');
                    if (+aYear !== +bYear) {
                        return +aYear - +bYear;
                    } else {
                        return +aMonth - +bMonth;
                    }
                });

                // Transform API data into the format suitable for the chart
                chartData = monthNames.map(monthName => {
                    const monthData = jsonData.filter(item => item.monthLabel === monthName);
                    return {
                        x: monthName,
                        'Andy Barnes': monthData.reduce((total, item) => total + item['Andy Barnes'], 0) / 1000000,
                        'Gary Williams': monthData.reduce((total, item) => total + item['Gary Williams'], 0) / 1000000,
                        'Bob Shaw': monthData.reduce((total, item) => total + item['Bob Shaw'], 0) / 1000000
                    };
                });

              
                // Refresh the chart with the updated data
                updateChart();
            } else {
                console.error('Failed to fetch data from the API. HTTP status:', response.status);
            }
        } catch (error) {
            console.error('An error occurred while fetching data:', error);
        }
    }
  
    // Function to create or update the chart
    function updateChart() {
        chartData = chartData.map(item => {
    const dateParts = item.x.split('/');
    const month = parseInt(dateParts[0]);
    const year = parseInt(dateParts[1]);

    // Convert the month number to a three-letter month abbreviation (e.g., Jan, Feb)
    const monthAbbreviation = new Date(year, month - 1, 1).toLocaleString('default', { month: 'short' });

    return {
        x: `${monthAbbreviation} ${year}`, 
        'Andy Barnes': item['Andy Barnes'],
        'Gary Williams': item['Gary Williams'],
        'Bob Shaw': item['Bob Shaw'],
    };
});

        // Create and append the chart with the updated data source
        const chart = new Chart({
            primaryXAxis: {
                valueType: 'Category',
                majorGridLines: { width: 0 },
           
                labelStyle: {
                    size: '15px',
                 fontWeight:"normal"
                },
                
            },
            primaryYAxis: {
                edgeLabelPlacement: 'Shift',
                majorTickLines: { width: 0 },
                minorTickLines: { width: 0 },
                lineStyle: { width: 0 },
                labelFormat: '{value}M',
                labelPlacement: 'OnTicks',
                minimum: 0, maximum: 5, interval:1,
                title: 'Placement value',
                titleStyle: {
                    fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                  fontWeight: 'Normal',
                  color: "#767676;",
                  size: '18px',
                 
                 },
                labelStyle: {
                    size: '15px',
                 fontWeight:"normal"
         
        
        },
            },
            width: '1350px', // Set the width of the chart
            height: '400px', // Set the height of the chart
            series: [
                {
                    type: 'StackingColumn',
                    dataSource: chartData,
                    xName: 'x',
                    width: 2,
                    yName: 'Bob Shaw',
                    columnSpacing: 0.2,
                    name: 'Bob Shaw',
                    fill: 'DodgerBlue',
                    columnWidth: 0.5,
                },
                {
                    type: 'StackingColumn',
                    dataSource: chartData,
                    xName: 'x',
                    width: 2,
                    yName: 'Andy Barnes',
                    columnSpacing: 0.2,
                    name: 'Andy Barnes',
                    fill: 'Tomato',
                    columnWidth: 0.5,
                },
                {
                    type: 'StackingColumn',
                    dataSource: chartData,
                    xName: 'x',
                    width: 2,
                    yName: 'Gary Williams',
                    columnSpacing: 0.2,
                    name: 'Gary Williams',
                    fill: 'MediumSeaGreen',
                    columnWidth: 0.5,
                },
            ],
        
            tooltip: {
                enable: true,
                format: 'GDP ${point.y}', // Customize tooltip format
                template:'#Female-Material'
               
              
            },
            legendSettings: { enableHighlight :true },

        });

        chart.appendTo('#container3');
    }

    onMount(() => {
      getDatesFromLocalStorage(); // Try to get dates from local storage
      fetchData(startDate, endDate); // Fetch data on component mount
    });
  
    afterUpdate(() => {
      fetchData(startDate, endDate); // Fetch data after updates
      updateChart(); // Update the chart immediately
  
      // Store the dates in local storage for future use
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    });
</script>
<div class="container1">
    <h1 class="h1">Placement Value by User</h1>
<div id='container3'></div>
</div>
<style>
       .container1{
        background-color:white;
        width: 1400px;
	    height: 430px;
        margin-left:40px;
        margin-top:80px;
       
    }
     .h1{
    font-size: 17px;
    font-weight: bold;
 
    margin-bottom: 20px;

 }
</style>