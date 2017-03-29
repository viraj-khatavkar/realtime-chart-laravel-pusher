<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <div class="panel panel-default">
                    <div class="panel-heading">Example Component</div>

                    <div class="panel-body">
                        <canvas id="myChart" height="100"></canvas>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import Chart from 'chart.js'

    export default {
        props: ['registered'],
        data() {
            return {
                count: [0, 0],
                labels: ['Registered', 'Online']
            }
        },
        mounted() {
            this.count[0] = this.registered;
            this.update();
            this.drawChart();
        },
        methods: {
            drawChart() {
                let ctx = document.getElementById("myChart");
                let myChart = new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: this.labels,
                        datasets: [{
                            label: '# of Users',
                            data: this.count,
                            borderWidth: 1
                        }]
                    },
                    options: {
                        scales: {
                            yAxes: [{
                                ticks: {
                                    beginAtZero:true
                                }
                            }]
                        }
                    }
                });
            },
            update() {
                Echo.join('chart')
                    .here((users) => {
                        this.count[1] = users.length;
                        this.drawChart();
                    })
                    .joining((user) => {
                        this.count[1]++;
                        this.drawChart();
                    })
                    .leaving((user) => {
                        this.count[1]--;
                        this.drawChart();
                    });
            }
        }
    }
</script>
