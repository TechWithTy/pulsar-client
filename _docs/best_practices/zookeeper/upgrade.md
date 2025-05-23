Upgrade Guide
Upgrade guidelines

Apache Pulsar is comprised of multiple components, ZooKeeper, bookies, and brokers. These components are either stateful or stateless. You do not have to upgrade ZooKeeper nodes unless you have special requirements. While you upgrade, you need to pay attention to bookies (stateful), brokers, and proxies (stateless).

Read the following guidelines before upgrading a Pulsar cluster.

    Back up all your configuration files before upgrading.
    Read the guide entirely, make a plan, and then execute the plan. When you make an upgrade plan, you need to take your specific requirements and environment into consideration.
    Pay attention to the upgrade sequence of components. In general, you do not need to upgrade your ZooKeeper or configuration store cluster (the global ZooKeeper cluster). You need to upgrade bookies first, and then upgrade brokers, proxies, and your clients.
    If autorecovery is enabled, you need to disable autorecovery in the upgrade process, and re-enable it after completing the process.
    Read the release notes carefully for each release. Release notes contain features and configuration changes that might impact your upgrade.
    Upgrade a small subset of nodes of each type to canary test the new version before upgrading all nodes of that type in the cluster. When you have upgraded the canary nodes, run for a while to ensure that they work correctly.
    Upgrade one data center to verify the new version before upgrading all data centers if your cluster runs in multi-cluster replicated mode.

note

Currently, Apache Pulsar is compatible between versions.
Upgrade sequence

To upgrade an Apache Pulsar cluster, follow the upgrade sequence.

    Upgrade ZooKeeper (optional).
        Canary test: test an upgraded version in one or a small set of ZooKeeper nodes.
        Rolling upgrade: roll out the upgraded version to all ZooKeeper servers incrementally, one at a time. Monitor your dashboard during the whole rolling upgrade process.

    Upgrade bookies.

        Canary test: test an upgraded version in one or a small set of bookies.

        Rolling upgrade:
            a. Disable autorecovery with the following command.

        bin/bookkeeper shell autorecovery -disable

    b. Roll out the upgraded version to all bookies in the cluster after you determine that a version is safe after canary.
    c. After you upgrade all bookies, re-enable autorecovery with the following command.

bin/bookkeeper shell autorecovery -enable

    Upgrade brokers.
        Canary test: test an upgraded version in one or a small set of brokers.
        Rolling upgrade: roll out the upgraded version to all brokers in the cluster after you determine that a version is safe after canary.

    Upgrade proxies.
        Canary test: test an upgraded version in one or a small set of proxies.
        Rolling upgrade: roll out the upgraded version to all proxies in the cluster after you determine that a version is safe after canary.

Upgrade ZooKeeper (optional)

While you upgrade ZooKeeper servers, you can do a canary test first, and then upgrade all ZooKeeper servers in the cluster.
Canary test

You can test an upgraded version in one of ZooKeeper servers before upgrading all ZooKeeper servers in your cluster.

To upgrade a ZooKeeper server to a new version, complete the following steps:

    Stop the ZooKeeper server.
    Upgrade the binary and configuration files.
    Start the ZooKeeper server with the new binary files.
    Use pulsar zookeeper-shell to connect to the newly upgraded ZooKeeper server and run a few commands to verify if it works as expected.
    Run the ZooKeeper server for a few days, observe and make sure the ZooKeeper cluster runs well.

tip

If issues occur during the canary test, you can shut down the problematic ZooKeeper node, revert the binary and configuration, and restart the ZooKeeper with the reverted binary.
Upgrade all ZooKeeper servers

After the canary test to upgrade one ZooKeeper in your cluster, you can upgrade all ZooKeeper servers in your cluster.

You can upgrade all ZooKeeper servers one by one by following the steps in the canary test.
Upgrade bookies

While you upgrade bookies, you can do a canary test first, and then upgrade all bookies in the cluster. For more details, you can read Apache BookKeeper Upgrade guide.
Canary test

You can test an upgraded version in one or a small set of bookies before upgrading all bookies in your cluster.

To upgrade a bookie to a new version, complete the following steps:

    Stop the bookie.

    Upgrade the binary and configuration files.

    Start the bookie in ReadOnly mode to verify if the bookie of this new version runs well for reading workload.

    bin/pulsar bookie --readOnly

When the bookie runs successfully in ReadOnly mode, stop the bookie and restart it in Write/Read mode.

bin/pulsar bookie

    Observe and make sure the cluster serves both write and read traffic.

tip

If issues occur during the canary test, you can shut down the problematic bookie node. Other bookies in the cluster replace this problematic bookie node with auto-recovery.
Upgrade all bookies

After the canary test to upgrade some bookies in your cluster, you can upgrade all bookies in your cluster.

Before upgrading, you have to decide whether to upgrade the whole cluster at once, including downtime and rolling upgrade scenarios.

In a rolling upgrade scenario, upgrade one bookie at a time. In a downtime upgrade scenario, shut down the entire cluster, upgrade each bookie, and then start the cluster.

While you upgrade in both scenarios, the procedure is the same for each bookie.

    Stop the bookie.
    Upgrade the software (either new binary or new configuration files).
    Start the bookie.

tip

When you upgrade a large BookKeeper cluster in a rolling upgrade scenario, upgrading one bookie at a time is slow. If you configure a rack-aware or region-aware placement policy, you can upgrade bookies rack by rack or region by region, which speeds up the whole upgrade process.
Upgrade brokers and proxies

The upgrade procedure for brokers and proxies is the same. Brokers and proxies are stateless, so upgrading the two services is easy.
Canary test

You can test an upgraded version in one or a small set of nodes before upgrading all nodes in your cluster.

To upgrade a broker (or proxy) to a new version, complete the following steps:

    Stop a broker (or proxy).
    Upgrade the binary and configuration file.
    Start a broker (or proxy).

tip

If issues occur during the canary test, you can shut down the problematic broker (or proxy) node. Revert to the old version and restart the broker (or proxy).
Upgrade all brokers or proxies

After the canary test to upgrade some brokers or proxies in your cluster, you can upgrade all brokers or proxies in your cluster.

Before upgrading, you have to decide whether to upgrade the whole cluster at once, including downtime and rolling upgrade scenarios.

In a rolling upgrade scenario, you can upgrade one broker or one proxy at a time if the size of the cluster is small. If your cluster is large, you can upgrade brokers or proxies in batches. When you upgrade a batch of brokers or proxies, make sure the remaining brokers and proxies in the cluster have enough capacity to handle the traffic during the upgrade.

In a downtime upgrade scenario, shut down the entire cluster, upgrade each broker or proxy, and then start the cluster.

While you upgrade in both scenarios, the procedure is the same for each broker or proxy.

    Stop the broker (or proxy).
    Upgrade the software (either new binary or new configuration files).
    Start the broker (or proxy).

tip

To check the health of the broker, you can use the following command or API.

    Admin CLI
    REST API

pulsar-admin brokers healthcheck
