
# Contribution Guidelines

## General
1. Commit message must include Signed-off-by: your-name \<your email> 
2. Message format
  * Use the first line as a high level description
  * provide a short paragraph describing the change - especially if its
    changing functionality, or adding a new feature.



## Ansible Playbooks

### Playbook Design Principles
1. Try to limit the variables set directly within a playbook - use vars/defaults where possible, to keep the main playbook clean and less confusing.
2. Try to keep ansible verbosity in check i.e. if you can exit clean, do so, don't leave the user wondering what happened.
3. If there are obvious and simple tests than can avoid a task failure, do them as early as possible and fail gracefully with meaningful error message(s).

### Playbook Changes
Must pass the following
```
# ansible-playbook <playbook-name> --syntax-check
# ansible-playbook <playbook-name> --check
```

## Grafana Dashboards

### Dashboard design Principles
1. Try to only consume grafana supplied panels
2. Defined a dashboard variable called datasource of type prometheus
3. Each panel **must** define the data source as ${datasource}
4. Keep the number of data series rendered to a minimum - *too many makes the panel unresponsive*
5. Where metrics aggregation is key, consider using recording rules to speed up rendering

### Dashboard Changes
1. Create a copy of the dashboard in the extras folder
2. Remember all datasource references must be ${datasource}...if not -> **REJECT**
3. Raise a PR to add your modified dashboard to the extras directory
    - this is where peer review from the wider team takes place
4. raise a subsequent PR to merge from 'extras' to ```roles/dashboard/files/*```

## Technology Preferences
- podman then docker
 

## Testing & Development Strategies

Testing the playbooks can be straight forward, but varies based on the deployment mode.

When making changes to the Openshift deployment playbook, you'll need a target OpenShift environment. The easiest way to stand up an OpenShift environment is probably [OpenShift Local](https://developers.redhat.com/products/openshift-local/overview). This allows you to deploy Grafana with the 'odf-grafana' dashboards and validate the Prometheus integration. 

Testing changes to the local deployment playbook just requires a Prometheus archive (`tar.gz`) file. This could cover any suitable time frame, but a minimum of 1 hour is recommended to align with the default 1 hour view used in most dashboards.

Local mode can also be used to test dashboard changes without needing all the resources required for ODF. If you can source a Prometheus archive from a running ODF environment, local mode's Prometheus instance will enable you to test dashboard changes without a running OCP/ODF cluster.
