# Part 2 ? Checklist (screenshots / proof)

- Jenkins controller running (Part 1 instance) on port 8080
- Node `ci-agent` online with label `ci`
- Node `docker-agent` online with label `docker`
- Credentials entry `demo-token` exists
- Pipeline job uses repo `task-management-api-part-2` and Jenkinsfile from SCM
- Build logs show:
  - stages on `ci-agent` (npm install/test)
  - stages on `docker-agent` (docker build/run)
  - successful health check
- Artifact `build-info.txt` visible under Build ? Artifacts

