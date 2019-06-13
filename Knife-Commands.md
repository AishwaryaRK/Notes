# Knife Commands

### Common cmds:

    knife node list
    knife node show
    knife node edit
    
    knife client delete 
    knife node delete 
    
    knife role show
    Knife role list

### data bag:

    knife data bag show mysql

### search nodes:

    knife search "tags:tag1\:val1 AND run_list:recipe\\[cookbook_name\:\:recipe_name\\]"

### bootstrap:

    knife bootstrap <ip> -E <env_production> -N <node_name> -x <user> --sudo -r "recipe[r1]" --bootstrap-version <chef_version>

### Edit/Add chef attributes on multiple nodes:

    #add tag
    knife exec -E 'nodes.transform("name:abc-*") {|n| puts n.tags.add("name:key"); n.save}'
    
    #add a recipe to run list
    knife exec -E 'nodes.transform("name:abc-*") {|n| puts n.run_list << "recipe[cookbook_name::recipe_name]"; n.save }'

### Run chef-client on multiple nodes:

    knife ssh 'chef:query' 'sudo chef-client -o recipe[a::b]' -x <user_name> -a ipaddress
    
    knife ssh "tags:tag1\:val1 AND run_list:recipe\\[cookbook_name\:\:recipe_name\\]" 'sudo chef-client -o "recipe[cookbook_name::recipe_name]"' -x <user>
