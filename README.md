---
- name: Install and Configure Nginx and Prometheus
  hosts: all
  become: true
  vars:
    ansible_user: "your_username"  # You can specify a default user here if needed
  
  tasks:
    # Install and Configure Nginx on Ubuntu
    - name: Install Nginx (Ubuntu)
      apt:
        name: nginx
        state: present
      when: ansible_facts['distribution'] == 'Ubuntu'

    # Install and Configure Nginx on CentOS
    - name: Install Nginx (CentOS)
      yum:
        name: nginx
        state: present
      when: ansible_facts['distribution'] == 'CentOS'

    # Configure Nginx on Ubuntu
    - name: Configure Nginx (Ubuntu)
      template:
        src: templates/nginx_config.j2
        dest: /etc/nginx/sites-available/default
      when: ansible_facts['distribution'] == 'Ubuntu'

    # Configure Nginx on CentOS
    - name: Configure Nginx (CentOS)
      copy:
        src: /etc/nginx/nginx.conf
        dest: /etc/nginx/nginx.conf
      when: ansible_facts['distribution'] == 'CentOS'

    # Ensure Nginx is started and enabled on Ubuntu
    - name: Ensure Nginx is started and enabled (Ubuntu)
      service:
        name: nginx
        state: started
        enabled: yes
      when: ansible_facts['distribution'] == 'Ubuntu'

    # Ensure Nginx is started and enabled on CentOS
    - name: Ensure Nginx is started and enabled (CentOS)
      service:
        name: nginx
        state: started
        enabled: yes
      when: ansible_facts['distribution'] == 'CentOS'

    # Install and Configure Prometheus on Ubuntu (separate server)
    - name: Install Prometheus (Ubuntu)
      apt:
        name: prometheus
        state: present
      when: "'prometheus_servers' in group_names and ansible_facts['distribution'] == 'Ubuntu'"

    # Install and Configure Prometheus on CentOS (separate server)
    - name: Install Prometheus (CentOS)
      yum:
        name: prometheus
        state: present
      when: "'prometheus_servers' in group_names and ansible_facts['distribution'] == 'CentOS'"

    # Ensure Prometheus is started and enabled on Ubuntu
    - name: Ensure Prometheus is started and enabled (Ubuntu)
      service:
        name: prometheus
        state: started
        enabled: yes
      when: "'prometheus_servers' in group_names and ansible_facts['distribution'] == 'Ubuntu'"

    # Ensure Prometheus is started and enabled on CentOS
    - name: Ensure Prometheus is started and enabled (CentOS)
      service:
        name: prometheus
        state: started
        enabled: yes
      when: "'prometheus_servers' in group_names and ansible_facts['distribution'] == 'CentOS'"

    # Change MOTD
    - name: Change MOTD
      lineinfile:
        path: /etc/motd
        line: "Ansible Managed by {{ ansible_user }}"
        create: yes
      notify:
        - Reboot Server (Optional for MOTD change)

  handlers:
    - name: Reboot Server
      reboot:
        reboot_timeout: 300
