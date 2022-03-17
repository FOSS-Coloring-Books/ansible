The Ansible Coloring Book
=================
## How I Learned to Stop Worrying and Love Automation

--------------------------------------------------------------

Systems Management
-----------------

  * [STORY] Meet AnsiBull. He is an astronaut commander and scientist. Using Ansible from a control machine aboard his rocket, he manages a vast interstellar network with tens of thousands of surface rovers that collect data for his mission to further our understanding of the universe. 
  * Some rovers are identical, or very similar, in design. Others are customized for the environments they survey. All must be kept updated with the latest data collection commands, configuration, software updates, and security updates that apply to their specific design. 
  * [STORY] AnsiBull's current project is provisioning a new set of ice-digging rovers to the moons of the gas giants in Earth's solar system.

SSH vs Agents
------------

   * The rovers are remote and may go many years without physical intervention, so stability and resource efficiency is critical. 
   * Rather than using an add-on agent to communicate with rovers, AnsiBull uses SSH. Since SSH is a secure communication protocol built in to each rover's operating system, no additional resources are required as they would be with an add-on agent.
   * All rovers have SSH built in natively, so no extra assembly is required for each rover's deployment. SSH is ubiquitous and is used across many computer systems. SSH uses strong encryption for secure communication between the control machine and individual rovers, even across insecure interstellar networks. Thanks to SSH, those mischevious Betelgeusians from Orion's Belt can't replace rover imaging data with furless Rigellian felinoids memes.


Inventories
----------

([documentation reference](http://docs.ansible.com/ansible/intro_inventory.html))
 
 * AnsiBull keeps track of thousands of rovers under his watch in an Ansible inventory.
 * AnsiBull has some of his rovers listed in a static inventory file. He creates his list using rover hostnames or IP addresses, and he lists his rovers logically into groups.
 
        [jupiter-rovers]
        io  
        europa
        ganymede 
        callisto
        
        [saturn-rovers]
        saturn-rover-*
       
        [rocky-rovers]
        io
        ganymede
        callisto
        saturn-rover-[01:03]
        
        [icy-rovers]
        europa
        ganymede
        callisto
        saturn-rover-[04:13]

        [moon-rovers:children]
        jupiter-rovers
        saturn-rovers
     
 * He stores some variables that apply to certain groups in his inventory file, as well:
 
        [*]
        image_storage_path = /media/images
      
        [icy-rovers:vars]
        ice_drill = enabled
        
        [rocky-rovers:vars]
        rock_drill = enabled
    
  * He can also have a dynamic inventory that updates automatically based on tags applied to systems in external places, like a cloud provider or LDAP.
    * ([documentation reference](http://docs.ansible.com/ansible/intro_dynamic_inventory.html))
    
Modules
-------

  * Now AnsiBull has his rovers neatly inventoried and organized. What does he do with them? How does he run commands, or modify configuration files, or install software on them? Modules!
  * Modules are Ansible plugins that execute tasks. For example:
    * The **`ping`** module is a simple diagnostic test to make sure a given rover can be contacted. If successful, a "pong" is returned.
    * The **`command`** module executes a command on the selected systems. For example, wget runs on a rover to download the latest mission waypoint data.
    * The **`service`** module restarts a service running on a rover. For example, httpd can be restarted after a configuration change.
    * The **`dnf`** module can install, upgrade, remove, and list software packages.
  * There are hundreds of modules available for Ansible! AnsiBull can even write his own. But how does he use them? Where do they run? 
  
Playbooks
--------

  * How does AnsiBull consistently manage complex multi-step configuration? How does he setup of thousands of rovers so quickly, and up to spec? Ansible playbooks!
  * A **play** is a set of tasks. A task is a set of actions, like updating a mission waypoint data package. These are completed with modules, like the yum module for installing packages. Plays are grouped into Ansible playbooks.
        
         ---
         # mission data update play
         
          - name: data collection templates
            hosts: all
            command: /bin/template-sync
         
          - name: waypoint data update
            hosts: saturn-rovers, jupiter-rovers, neptune-rovers, uranus-rovers
            yum: 
              name: waypoint-data
              state: latest
            
  * Although every task can be stored in one big playbook file, it makes sense to break out tasks, or even entire plays, into smaller chunks so they can be shared across playbooks, or even with other missions. 

State
-----

  * [STORY] This little rover (saturn-rover-05) fell in a ditch while receiving a mission update from a playbook run. It has temporarily lost its network connection. Oh no! What will happen?
  * Traditionally, system administrators like AnsiBull would manage a set of systems, trying to script everything the system should do. Ansible has a focus on system state. It does the bare minimum to get the system to the state specified by the playbook.
  * If a task in a playbook fails to run on a particular system, it's exempt from the remaining tasks in the playbook on that run. 
  * AnsiBull then receives a status notification that saturn-rover-05 didn't complete the playbook. He runs the playbook again so that any systems that happened to fall in a ditch or get attacked by a Rigellian felinoid catches up on anything they missed. Rovers that already successfully completed all the tasks in the playbook's plays aren't affected.
  * Ansible is a different way of approaching system management. It focuses on the state that a system must be in; it provides an ice planet rover, a fully-updated rover, and so on. Actions needing to be run to get rovers to those states are handled at a lower level, in tasks and plays.

  
Facts
-----

  * [Story] AnsiBull tries to run the playbook on the rover again, but receives a notification that an error occurred. The ice drill on the rover can't be enabled... it was damaged in the cliff fall. Oops.
  * AnsiBull uses AnsiBull's interactive command interface to debug the ice drill remotely, but can't figure it out.
  * [Story] Time to call ice drill customer support for help! Uh-oh. Is the drill still under warranty? Oh, and what brand drill is it? Who should AnsiBull call?
  * Ansible has a feature that gathers facts about systems. Some facts are gathered automatically based on information provided by the system. Some facts are custom values created by scripts. 
  * Using the facts system, AnsiBull finds the right hardware vendor, the serial number for the drill, and the warranty lapse date. It's still under warranty! He calls the vendor and finds out there's been a recall on the drill.
  * Which rovers have this kind of ice drill? Using Ansible facts, he creates a group of all rovers with that drill model, gets the list of serial numbers, and orders replacement drills from customer support.
  * He also generates a list of the locations of all recalled drills so he can plan out his trips to install replacements.
  
Conclusion
----------
  
  * Thanks to Ansible, a drill disaster has turned into an easy fix.
  * [Story] The Ansible has finished his ice-digging rover deployments, and is ready to move on to other star systems.
  * To learn more about Ansible, please visit http://ansible.com
  
