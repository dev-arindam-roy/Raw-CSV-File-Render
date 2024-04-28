# RAW CSV FILE DATA RENDERING

## Application with raw csv file data

### Check Application
[https://dev-arindam-roy.github.io/Raw-Json-File-Render/](https://dev-arindam-roy.github.io/Raw-Json-File-Render/)

```js
    const callObj = {
        name: "Dummy 100 Customers",
        url: "./csvs/customers-100.csv" 
    };

    const csvTable = (dataArr, renderId, appName = '') => {

        /* create table */
        let _table = document.createElement('table');
        _table.setAttribute('id', 'dataTable');
        _table.classList.add('table', 'table-bordered', 'table-striped', 'table-sm');
        
        if (dataArr.length) {

            /* remove last array item*/
            dataArr.splice(-1);

            let headerArr = dataArr[0];
            let dataLength = parseInt(headerArr.length);

            /* create thead & apped to table */
            let _thead = document.createElement('thead');
            _table.appendChild(_thead);

            if (appName !== '') {
                /* create heading row with colspan td */
                let _headingTr = _thead.insertRow();
                let _headingTd = _headingTr.insertCell(0);
                _headingTd.colSpan = dataLength;
                _headingTd.innerHTML = `<strong>${appName}</strong> (${dataArr.length - 1})`;
            }

            dataArr.map((value, index) => {

                if (index === 0) {
                    /* add or insert tr within thead */
                    let _headTr = _thead.insertRow();

                    /* dynamically create th & append to above tr*/
                    for (let i = 0; i < dataLength; i++) {
                        let _headTh = document.createElement('th');
                        let _headThText = document.createTextNode(`${headerArr[i].charAt(0).toUpperCase()}${headerArr[i].slice(1)}`);
                        _headTh.appendChild(_headThText);
                        _headTr.appendChild(_headTh);
                    }   
                } else {
                    /* create tbody & append to table */
                    let _tbody = document.createElement('tbody');
                        _table.appendChild(_tbody);

                    let _tr = _tbody.insertRow();
                    
                    /* dynamically create td with object value & append to above tr in map loop*/
                    for (let i = 0; i < dataLength; i++) {
                        let _td = _tr.insertCell(i);

                        /* convert to string and replace doubble quotes*/
                        let _tdText = document.createTextNode(`${String(value[i]).replace(/^"|"$/g, '')}`);
                        _td.appendChild(_tdText);
                    }
                }
            });
            /* finally table render to html */
            document.getElementById(renderId).appendChild(_table);
        } else {
            document.getElementById(renderId).innerHTML = `<h4>No Records Found!</h4>`;
        }
    }

    const fetchData = async () => {
        await fetch(callObj.url).then((response) => {
            return response.text();
        }).then((data) => {
            let resultData = data.split(/\r?\n|\r/).map((e) => {
                return e.split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/);
            });
            console.log(resultData);
            csvTable(resultData, 'dataRender', callObj.name);
        }).catch((error) => {
            console.log(error);
        });
    };
    fetchData();

    document.getElementById('downloadBtn').addEventListener('click', downloadAsCsv);

    function downloadAsCsv() {
        let csvContent = tableToCsv();
        let csvFile = new Blob([csvContent], { type: "text/csv" });
        let tempLink = document.createElement('a');
        tempLink.download = new Date().toISOString() + '.csv';
        let url = window.URL.createObjectURL(csvFile);
        tempLink.href = url;
        tempLink.style.display = "none";
        document.body.appendChild(tempLink);
        tempLink.click();
        document.body.removeChild(tempLink);
    }

    function tableToCsv() {
        let csvData = [];
        let rows = document.getElementsByTagName('tr');
        for (let i = 0; i < rows.length; i++) {
            let cols = rows[i].querySelectorAll('td,th');
            let csvrow = [];
            for (let j = 0; j < cols.length; j++) {
                csvrow.push(cols[j].innerHTML);
            }
            csvData.push(csvrow.join(","));
        }
        csvData = csvData.join('\n');  
        return csvData;

    }
```