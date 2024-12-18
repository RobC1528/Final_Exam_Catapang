---
- name: Setup Apache and Netdata on CentOS and Ubuntu servers
  hosts: all
  become: true
  tasks:

    # Install Apache (for Ubuntu and CentOS)
    - name: Install Apache on Ubuntu and CentOS
      package:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == 'Debian'

    - name: Install Apache on CentOS
      package:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == 'RedHat'

    # Start and enable Apache service (for Ubuntu and CentOS)
    - name: Start and enable Apache service
      service:
        name: "{{ 'apache2' if ansible_facts['os_family'] == 'Debian' else 'httpd' }}"
        state: started
        enabled: yes
      when: ansible_facts['os_family'] == 'Debian' or ansible_facts['os_family'] == 'RedHat'

    # Install Netdata (for Ubuntu and CentOS)
    - name: Install dependencies for Netdata (Ubuntu and CentOS)
      package:
        name:
          - curl
          - gcc
          - make
          - zlib1g-dev
          - libuuid1
          - libmnl-dev
        state: present
      when: ansible_facts['os_family'] == 'Debian' or ansible_facts['os_family'] == 'RedHat'

    # Install Netdata (for Ubuntu)
    - name: Install Netdata on Ubuntu
      shell: |
        bash <(curl -Ss https://my-netdata.io/kickstart.sh)
      when: ansible_facts['os_family'] == 'Debian'

    # Install Netdata (for CentOS)
    - name: Install Netdata on CentOS
      shell: |
        bash <(curl -Ss https://my-netdata.io/kickstart.sh)
      when: ansible_facts['os_family'] == 'RedHat'

    # Start and enable Netdata service (for Ubuntu and CentOS)
    - name: Start and enable Netdata service
      service:
        name: netdata
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
