php-resque-influxdb: PHP Resque InfluxDB
==========================================

php-resque-influxdb implements InfluxDB metric tracking into php-resque.

For each job picked up by php-resque, a point will be submitted to
InfluxDB, including tags to track the number of jobs executed, and timers to
track how much time php-resque workers spend working.

php-resque-influxdb also includes support for tracking metrics for jobs scheduled
with [php-resque-scheduler](http://github.com/chrisboulton/php-resque-scheduler).
The appropriate listeners to track scheduled jobs are automatically registered,
so no extra work is required on your behalf.

## Using php-resque-influxdb

php-resque-influxdb exists as a single class (`lib/InfluxDBLogger.php`), which has
no additional dependencies beyond php-resque itself.

To start tracking your jobs with InfluxDB, all you need to do is include
`InfluxDBLogger.php` in your project with PSR-4. The namespace is `\Resque\Logging`.
After this you can run `\Resque\Logging\InfluxDBLogger::register()`

### InfluxDB Connection Details

php-resque-scheduler will automatically check for the following environment
variables if they exist and use them when connecting to InfluxDB:

 * `INFLUXDB_HOST`
 * `INFLUXDB_PORT`
 * `INFLUXDB_USERNAME`
 * `INFLUXDB_PASSWORD`

To ease integration with existing setups, if `INFLUXDB_HOST` includes
a single colon and then one or more numbers, this will be interpretted
as a HOST:PORT combination and both the host and port will be set accordingly.

If you don't use environment variables in your project, you can still tell
php-resque-influxdb where InfluxDB is located:

	$host = '127.0.0.1';
	$port = 8579;

	// Automatically register if Resque is available
    if (class_exists('Resque') && !defined('RESQUEINFLUXDB_DONT_REGISTER'))
    {
        \Resque\Logging\InfluxDBLogger::register();
    }
	\Resque\Logging\InfluxDBLogger::setHost($host, $port);


## Metrics

php-resque-influxdb send all metrics it generates to the DB `resque`. You can
override this behavior if desired:

	\Resque\Logging\InfluxDBLogger::register();
	\Resque\Logging\InfluxDBLogger::setDB('resque.production');
	
Metrics that are send have the following fields for a successful job:

    'execution_time' => $executionTime,
    'queue_time'     => $job->influxDBTimeInQueue OR 'null',
    'start_time'     => $job->influxDBStartTime  OR 'null',
	
and add the following field for an unsuccessful job:

    'error' => $e->getMessage()

And feature the job class, queue and result as tags for the metric.

## Contributors ##

* chrisboulton
* SMillerDev