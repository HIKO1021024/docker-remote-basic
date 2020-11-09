FROM ubuntu:18.04

RUN apt-get update -y

RUN apt-get install sudo -y

RUN sudo  apt-get install -y nodejs=8.10.0~dfsg-2ubuntu0.4 npm=3.5.2-0ubuntu4
RUN sudo apt-get install -y curl git sudo apt locales-all software-properties-common \
	&& sudo apt-get clean 

RUN sudo apt-get -y update \  
	&& sudo apt-get -y install wget apt-transport-https \
	&& sudo wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb \
	&& sudo dpkg -i packages-microsoft-prod.deb \
	&& curl -sSL https://packages.microsoft.com/keys/microsoft.asc -o microsoft.asc \
	&& sudo apt-key add microsoft.asc \
	&& echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"  | sudo tee /etc/apt/sources.list.d/vscode.list \
	&& sudo add-apt-repository universe \
	&& sudo apt-get -y update \
	&& sudo apt-get -y install dotnet-sdk-3.1 aspnetcore-runtime-3.1 dotnet-runtime-3.1
	
RUN sudo apt-get install aptitude -y

RUN sudo apt install python3-pip -y
RUN sudo pip3 install awscli

RUN sudo npm install -g typescript@3.8.3

RUN export PATH=$PATH://usr/local/lib
RUN echo "export PATH=$PATH://usr/local/lib" >> /etc/profile

RUN sudo aptitude install ssh -y
RUN echo 'root:password' | chpasswd

RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
RUN sed -i 's/#   StrictHostKeyChecking ask/StrictHostKeyChecking no/' /etc/ssh/ssh_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd


# 手元の公開鍵をコピー
RUN mkdir -p /root/authorized_keys
COPY id_rsa.pub /root/authorized_keys

# ssh用の port を晒す
EXPOSE 22

WORKDIR /workspace

# 公開鍵を使えるようにする (パーミッション変更など)
CMD mkdir -p ~/.ssh && \
    mkdir -p /run/sshd && \
    chmod 0755 /run/sshd && \
    mv ~/authorized_keys ~/.ssh/authorized_keys && \
    chmod 0700 ~/.ssh &&  \
    chmod 0600 ~/.ssh/authorized_keys &&  \
    /usr/sbin/sshd -D
