# static_analysis_kernel
Reusable Github Actions for Linux kernel tests

This workload is given a pull_request metadata
and executes static analysis over the new HEAD.

Currently this workload should:
 1. Run checkpatch.pl on new commits
 2. report any coccicheck differences between base and head
 3. report any smatch differences between base and head
 4. build and upload the built kernel as an rpm


This workflow is cache [crazy](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/caching-dependencies-to-speed-up-workflows). This is because the kernel is big and we never want to
do anything twice. The HEAD sha is used as a key in most cases, meaning subsequent runs should
be quicker. We also use caches to allow for more parallelism.

### To run locally

First define a file to simulate a PR:

act_file:
```
{
  "pull_request": {
    "commits": 5,
    "head": {
      "ref": "nchild_gha_head",
      "repo": "cornelisnetworks/wfr-linux-devel",
      "sha": "8c6081b86be0ab9a65520d116eee16ec65be6aeb"
    },
    "base": {
      "ref": "nchild_gha_base",
      "repo": "cornelisnetworks/wfr-linux-devel",
      "sha": "ccabe93a3ca38d3059a059ea8b30ff25115bfab2"
    }
  }
}
```

Then we install [act](https://github.com/nektos/act#) to
run Github Actions locally.

I install `act` and cd into my linux source tree:

```
 sudo act --container-daemon-socket 'unix:///run/podman/podman.sock' -W <path_to_this_repo>/.github/workflows/ -e <path_to_act_file> pull_request
```

Some hacking like using `sudo` and setting the daemon socket is needed to work with podman (docker is expected). I also had to `ln -s /usr/bin/podman /usr/bin/docker`.

### To plugin to your linux source tree
Add a kernel build `config` file to your tree's `.github/actions/` directory.
In an action, call:
<TODO>
