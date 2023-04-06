FROM python:3.8-slim-buster

RUN apt-get update && apt-get -y install cron

# Copy job file to the cron.d directory
COPY job /etc/cron.d/job
COPY app.py /usr/src/app/app.py
 
# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/job

# Apply cron job
RUN crontab /etc/cron.d/job
 
# Create the log file to be able to run tail
RUN touch /var/log/cron.log
 
# Run the command on container startup
CMD cron && tail -f /var/log/cron.log
