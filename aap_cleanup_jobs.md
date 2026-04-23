# Cleaning up Ansible controller jobs via API

Usage:


```shell
ansible-playbook cleanup_aap_jobs.yml \
    -e "aap_host=https://aap.example.com" \
    -e "aap_token=<your_token>" \
    -e "retention_days=30" \
    -e "batch_size=100"
```

## How the loop works

batch_remaining starts at -1 so the when condition (!= 0) is true on the first iteration. After delete_batch.yml runs, it gets set to the API's count value — the total still beyond cutoff. Since we deleted batch_size jobs, count drops each iteration. When it hits 0, the when condition fails and Ansible skips all remaining loop iterations cleanly.

The range(0, 10000) is just a ceiling so the loop has something to iterate over — Ansible's include_tasks doesn't support until natively, so this is the standard pattern for a do-while equivalent.