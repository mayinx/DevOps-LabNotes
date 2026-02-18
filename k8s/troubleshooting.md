# 🚑 The K8s Emergency Room: Troubleshooting Guide

When `kubectl get pods` shows anything other than `Running`, follow this manual.

---

## 🚨 1. CrashLoopBackOff
**The Symptom:** The pod starts, runs for 5 seconds, and dies. K8s keeps restarting it.
* **Cause:** Usually a code bug, missing Env variable, or failed DB connection.
* **Fix:** 1. Check logs: `kubectl logs [pod-name] --previous` (Crucial: `--previous` sees the log *before* it crashed).
    2. Check Env: `kubectl exec [pod-name] -- env`

## 🚨 2. ImagePullBackOff
**The Symptom:** Pod won't start; status stays in "ImagePullBackOff."
* **Cause:** Typo in image name, wrong tag, or private registry credentials missing.
* **Fix:** 1. `kubectl describe pod [pod-name]` -> Look at "Events" at the bottom.
    2. Check `imagePullSecrets` in your Deployment YAML.

## 🚨 3. Pending
**The Symptom:** Pod is created but never starts.
* **Cause:** Cluster is full (CPU/RAM) or "Taints/Tolerations" prevent it from landing on a node.
* **Fix:**
    1. `kubectl describe pod [pod-name]`
    2. Look for: `FailedScheduling`. If it says "Insufficient cpu," you need a **Cluster Autoscaler** or to delete old pods.

## 🚨 4. NodePort / Service Connection Refused
**The Symptom:** Pod is `Running`, but you can't reach it.
* **Fix Checklist:**
    1. **Endpoints:** `kubectl get ep [svc-name]`. If empty, your `selector` labels are wrong.
    2. **Binding:** Ensure app is listening on `0.0.0.0` (not `127.0.0.1`).
    3. **TargetPort:** Ensure Service `targetPort` matches Pod `containerPort`.