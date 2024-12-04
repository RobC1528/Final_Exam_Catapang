---
- name: Install and Configure Nginx and Prometheus
  hosts: all
  become: true
  vars:
    ansible_user: "your_username"
  
  tasks:
    # Install and Configure Nginx
    - name: Install Nginx (Debian/Ubuntu)
      apt:
        name: nginx
        state: present
      when: ansible_facts['os_family'] == 'Debian'

    - name: Install Nginx (CentOS/RHEL)
      yum:
        name: nginx
        state: present
      when: ansible_facts['os_family'] == 'RedHat'

    - name: Configure Nginx
      template:
        src: templates/nginx_config.j2
        dest: /etc/nginx/sites-available/default
      when: ansible_facts['os_family'] == 'Debian'

    - name: Configure Nginx (CentOS/RHEL)
      copy:
        src: /etc/nginx/nginx.conf
        dest: /etc/nginx/nginx.conf
      when: ansible_facts['os_family'] == 'RedHat'

    - name: Ensure Nginx is started and enabled
      service:
        name: nginx
        state: started
        enabled: yes

    # Install and Configure Prometheus (on a separate server)
    - name: Install Prometheus (Debian/Ubuntu)
      apt:
        name: prometheus
        state: present
      when: "'prometheus_servers' in group_names and ansible_facts['os_family'] == 'Debian'"

    - name: Install Prometheus (CentOS/RHEL)
      yum:
        name: prometheus
        state: present
      when: "'prometheus_servers' in group_names and ansible_facts['os_family'] == 'RedHat'"

    - name: Ensure Prometheus is started and enabled
      service:
        name: prometheus
        state: started
        enabled: yes
      when: "'prometheus_servers' in group_names"

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
