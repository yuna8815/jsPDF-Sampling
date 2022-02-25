<template>
    <section class="page">
        <h1>jsPDF 샘플링</h1>
        <p>다운로드 버튼을 누르면 pdf파일이 다운로드됩니다.</p>
    
        <button @click="downloadJspdf">다운로드</button>
        
        <hr />

        <h3>Table 미리보기</h3>
        <table>
            <tr>
                <th>Category</th>
                <th>Revenue</th>
                <th>Change</th>
            </tr>
            <tr>
                <td>Widgets</td>
                <td>$1,298,471.00</td>
                <td>+32%</td>
            </tr>
            <tr>
                <td>Services</td>
                <td>$265,402.12</td>
                <td>+30%</td>
            </tr>
            <tr>
                <td>Licensing</td>
                <td>$28,000.00</td>
                <td>-28%</td>
            </tr>
        </table>

        <h3>Chart 미리보기</h3>
        <div class="chart-gird">
            <div class="chart"><GChart
                type="ColumnChart"
                :data="chartData"
                :options="chartOptions"
                @ready="onChartReady"
            /></div>
            <div class="chart"><GChart
                type="PieChart"
                :data="pieChartData"
                :options="pieChartOptions"
                @ready="onPieChartReady"
            /></div>
        </div>
    </section>
</template>

<script>
import { jsPDF } from "jspdf";
import 'jspdf-autotable';
import { GChart } from 'vue-google-charts'

import "../assets/fonts/NanumGothic-Regular-normal";
import "../assets/fonts/NanumGothic-Bold-normal";

export default {
    components: {GChart},
    data: () => ({
        chart: null,
        chartData: [
          ['Task', 'Last month', 'This month'],
          ['Widgets', 1298471, 1298471*1.32 ],
          ['Services', 265402, 265402*1.3 ],
          ['Licensing', 28000, 28000*1.28-28000 ],
        ],
        chartOptions: {
            chartArea: {
                left: 30
            },
            colors: ['#5047e5','#ec4899'],
            vAxis: {format: 'short'}
        },
        pieChart: null,
        pieChartData: [
          ['Category', 'Revenue'],
          ['Widgets', 1298471 ],
          ['Services', 265402 ],
          ['Licensing', 28000 ],
        ],
        pieChartOptions: {
            chartArea: {
                width:'90%',
                height: '90%'
            },
            colors: ['#5047e5','#ec4899','#c8d3fd'],
            fontSize: 12,
            pieSliceBorderColor: 'transparent'
        }
    }),
    methods: {
      createPdfContents(doc) {
        doc.setFont('NanumGothic-Bold');
        doc.setFontSize(24);
        doc.text('월간 판매 보고서', 214, 70);

        doc.setFont('NanumGothic-Regular');
        doc.setFontSize(12);

        doc.autoTable({
            // theme: 'grid',
            headStyles:  { halign: 'left', valign: 'middle' ,font : 'NanumGothic-Bold', fontStyle :'bold'},  //헤더 부분 옵션
            footStyles:  { halign: 'left', valign: 'middle' ,font : 'NanumGothic-Bold', fontStyle :'bold', fillColor:[245,245,245], textColor: '#000'},  //헤더 부분 옵션
            // startX: 24, 
            startY: 103,
            margin: {  left : 24, top : 0, right : 24  },  //여백
            // tableWidth : 테이블넓이,
            styles : { font : 'NanumGothic-Regular', fontStyle :'normal'},  //폰트적용
            head: [['Category', 'Revenue', 'Change']],
            body : [
                [
                    {content: 'Widgets', styles: {fillColor: '#fff'}},
                    {content: '$1,298,471.00', styles: {fillColor: '#fff'}},
                    {content: '+32%', styles: {fillColor: '#fff'}},
                ],
                [
                    {content: 'Services', styles: {fillColor: '#fff'}},
                    {content: '$265,402.12', styles: {fillColor: '#fff'}},
                    {content: '+30%', styles: {fillColor: '#fff'}},
                ],
                [
                    {content: 'Licensing', styles: {fillColor: '#fff'}},
                    {content: '$28,000.00', styles: {fillColor: '#fff'}},
                    {content: '-28%', styles: {fillColor: '#fff'}},
                ],
            ],
            foot: [
                [
                    {content: 'Total:'},
                    {content: '$1,591,873.12'},
                    {content: '+30%'},
                ],
            ]
        });

        doc.setFont('NanumGothic-Bold');
        doc.text('Revenue Sources', 110, 298)
        doc.addImage(this.pieChart.getImageURI(), 'PNG', 10, 320);
        doc.text('Month-of-Month', 385, 298)
        doc.addImage(this.chart.getImageURI(), 'PNG', 300, 320);
      },
      onChartReady(chart) {
        this.chart = chart;
      },
      onPieChartReady(chart) {
        this.pieChart = chart;
      },
      downloadJspdf() {
        var doc = new jsPDF('p','pt','a4');
        this.createPdfContents(doc);
        doc.save("jsPdf.pdf");
        doc = null;
      },
  }
}
</script>

<style>
.chart-gird {
    display: flex;
}
.chart {
    width: 360px;
}

h1 {margin-bottom: 0;}

table th,
table td {
    padding: 4px 12px;
}

table th {
    color: #fff;
    background: lightskyblue;
}

button {
    height: 36px;
    padding: 0 12px;
    border: none;
    border-radius: 4px;
    color: #fff;
    background: lightslategray;
}

button + button {margin-left: 20px;}
</style>