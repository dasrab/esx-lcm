# Overview

This file details how one can exercise lifecycle operations for VMware
platform in iterative way i.e. multiple times. The supported lifecycle
operations are: deploy, flex, update and delete. The platform deployment
involves end to end operation including VSAN cluster. Being NCS an
API oriented infrastrtucure managemetn platform, the focus is to carry
out end-to-end platform operation at following level:

>1. Usage of ESX LCM API for lifecycle operations
>2. Usage of NCS API for lifecycle operations

To start with, first option will be supported. See below sections for
more detail.

# Purpose

The goal of carrying out automated lifecycle operation of platform in
iterative way are following:

>1. Carry out end-to-end functionality
>2. Discover integration issues
>3. Discover stability of components of produce e.g. OneView, vCenter
>4. Discover timing issues
>5. Provide a tool to QE and Development team to do sanity check for new features
>6. Simulate failure scenarios and discover the system response


# Usage of ESX LCM API for lifecycle operations

This section explains how we will carry out platform lifecycle operation
in iterative way using ESX LCM API. Entry point for all operations will
be ESX LCM API instead of NCS API. To recap, ESX LCM API is set of REST
calls operating at zone level to support various platform lifecycle
operations. The ESX LCM is deployed as REST service in container.

### Components needed

>1. HPE Synergy hardware
>2. ESX LCM
>3. Mocked Appliance Manager

### Steps to perform the operations

The steps are categorised in two groups: manual and automated. Manual
steps are one time operations which is needed to be carried out to prepare
the test environment.

#### Manual steps (to be carried out ONCE only)

>* Populate the devops.conf with the same dummy ID used for creating the Zone.
>* Update file ../esx-lcm/ism-recipes/playbooks/properties/appliance_manager_properties.yml
    with following details:

<pre><code>
       appliance_manager_ip: localhost  # Dummy webserver to mock appliance manager
       appliance_manager_port:"5000"
       platform_profile_template_file_path:"/root/templates/platform_profile_template.json"
</code></pre>


>* Update file ../esx-lcm/ism-recipes/playbooks/properties/cluster_configuration_parameters.yml
    with below details:

<pre><code>
        datacenter_path:<DataCenterName>
        hostPassword:<HostPassword>
</code></pre>

>* Update the OSDeploymentPlanName in file ../esx-lcm/ism-recipes/templates/SY480_gen9.json



>* Update the resource profile templates: ../esx-lcm/ism-recipes/templates/platform_profile_template.json"
    with SY480 - Gen9 - Low - Density - Internal

>* Create Zone. Perform a post call with following request body. It does
   following: (1) Create a zone (2) Registers an SPT bluebrint by invoking
   appliance manager and (3) Stores SPT URI returned by appliance manager
   in DB


<pre><code>
    {
         # This can be a dummy id
         "id": "a46556e5-992f-11e7-a8cf-0242ac110099",
         "managed": true,
         "platformProfileName": <configured platform profile name>,
         "networkSettings": {
            "managementNetwork": <management network name from oneview>,
            "storageNetwork": <storage nwtwork name from oneview>,
            "movementNetwork": <vmotion network name from oneview>,
            "productionNetwork": <tenant network name from oneview>,
            "faultToleranceNetwork": <FT network name from oneview>,
            "deploymentNetwork": <deployment network from oneview>
         }
    }
</code></pre>




#### Automaed steps

Call below cli to deploy VMware platform.














# Appendix

>1. API to add zone capacity

### API to add zone capacity

Step: Do a PUT call.
URL: rest/zones/<zone_uuid>/AddCapacity
Request body:

<pre><code>
{
        "resourceCapacity": < Configurable >,
        "clusters": [{"name": Test - Cluster}],
        "oneViewSettings": {
            "ipAddress": < Configurable >,
            "username": < Configurable >,
            "password": < Configurable >
        },
        "vcenterSettings": {
            "ipAddress": < Configurable >,
            "username": < Configurable >,
            "password": < Configurable >
        },
        "regionURI": < Configurable >, # Dummy Name
        "providerURI": < Configurable >,# Dummy Name
        "osRegionName": << Configurable >  # Dummy Name
 }
</code>
</pre>


