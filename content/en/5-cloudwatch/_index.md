---
title: "Chapter 5: Monitoring with Amazon CloudWatch"
weight: 5
---

## Objectives

In this chapter, you will learn how to use **Amazon CloudWatch** to monitor the performance of containers running on ECS Fargate. Steps include:

- Monitor system metrics (CPU, memory, network)
- Create alarms
- Build visual dashboards
- (Optional) View logs for troubleshooting

---

## 1. Monitor metrics with CloudWatch Metrics

### Steps:

1. Access AWS Console → CloudWatch → **Metrics**
2. Find your ECS Cluster → select metrics to monitor:

| Metric | Meaning |
|--------|--------|
| `CPUUtilization` | CPU usage percentage |
| `MemoryUtilization` | Memory usage |

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/cloudwatch-metrics.png)

---

## 2. Create alerts with CloudWatch Alarm

### Example: Alert when CPU > 80% for 5 minutes

1. Go to CloudWatch → **Alarms → Create Alarm**
2. Select metric: `CPUUtilization`
3. Select cluster → service to monitor
4. Set conditions:
   - Threshold: `Greater than 80`
   - Evaluation: `5 minutes`
5. Optional: send alerts via SNS or email
6. Preview configuration → select "Create Alarm"

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/select-alarm-metrics.png)

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/select-alarm-condition.png)

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/create-alarm-success.png)

> **Tip**: Create additional alarms for `MemoryUtilization` if you suspect memory leaks.

---

## 3. Create overall monitoring dashboard

1. Go to CloudWatch → **Dashboards → Create Dashboard**
2. Name example: `ecs-monitoring-dashboard`
3. Add the following widgets:

| Widget | Type |
|--------|------|
| CPUUtilization (FE + BE) | Line graph |
| MemoryUtilization | Line graph |
| Alarm status | Text or Alarm widget |

4. Save and use for visual monitoring

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/cloudwatch-dashboard.png)

---

## 4. Check container logs

If you need to check errors:

1. Go to CloudWatch → Logs → Log groups
2. Select log groups to view
3. Click latest log stream → view detailed logs

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/log-groups.png)

> 🔧 Log driver is configured by default in task definition from previous step.

---

## Conclusion

Using CloudWatch helps you monitor, alert, and visualize ECS service performance. This is an essential DevOps tool to ensure applications run stably and respond promptly when issues occur.
