---
- name: Setup Nginx and Prometheus on CentOS and Ubuntu servers
  hosts: all
  become: true
  tasks:

    # Install Nginx (for Ubuntu and CentOS)
    - name: Install Nginx on Ubuntu and CentOS
      package:
        name: nginx
        state: present
      when: ansible_facts['os_family'] == 'Debian' or ansible_facts['os_family'] == 'RedHat'

    # Start and enable Nginx (for Ubuntu and CentOS)
    - name: Start and enable Nginx service
      service:
        name: nginx
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == 'Debian' or ansible_facts['os_family'] == 'RedHat'

    # Install Prometheus (for Ubuntu and CentOS)
    - name: Install Prometheus on Ubuntu and CentOS
      package:
        name: prometheus
        state: present
      when: ansible_facts['os_family'] == 'Debian' or ansible_facts['os_family'] == 'RedHat'

    # Start and enable Prometheus service (for Ubuntu and CentOS)
    - name: Start and enable Prometheus service
      service:
        name: prometheus
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == 'Debian' or ansible_facts['os_family'] == 'RedHat'

    # Change MOTD message (for Ubuntu and CentOS)
    - name: Change MOTD to "Ansible Managed by {{ ansible_user }}"
      lineinfile:
        path: /etc/motd
        line: "Ansible Managed by {{ ansible_user }}"
        create: yes
        state: present
