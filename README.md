## Introdução

Este repositório contém um modelo de arquivo autoinstall.yaml para realizar instalações automatizadas do Ubuntu utilizando o instalador Subiquity. O autoinstall é a nova forma (a partir do Ubuntu Server 20.04 e Ubuntu Desktop 23.04) de executar uma instalação "unattended", ou seja, sem intervenção manual, por meio de um arquivo de configuração em formato YAML.

Este arquivo permite pré-configurar diversos aspectos da instalação, como:
- Identidade e Usuário: Criação do usuário administrador com nome, senha (hash segura) e hostname.
- Localidade e Teclado: Configuração do idioma, fuso horário e layout do teclado.
- Rede: Definição dos parâmetros de rede (endereço IP, gateway e DNS).
- Pacotes e Snaps: Instalação de pacotes via apt e de aplicativos via snap.
- Codecs e Drivers: Instalação de codecs multimídia e drivers de terceiros.
- Atualizações e Desligamento: Configuração do tipo de atualização (instalar todas) e política de desligamento/reboot ao final.

> Este template foi desenvolvido pensando num ambiente de desenvolvimento, incluindo ferramentas comuns como Docker, Node.js, Git, VSCode (snap "code"), Slack, Obsidian, Postman, entre outros.

### Estrutura do Arquivo `autoinstall.yaml`

A seguir, explicamos cada seção do arquivo:

1. Cabeçalho e Versão

```yaml
#cloud-config
autoinstall:
  version: 1
```
- #cloud-config: Indica que o arquivo será processado pelo cloud-init.
- version: Define a versão do formato do arquivo (atualmente deve ser 1).

2. Identidade e Configuração do Sistema

```yaml
  identity:
    realname: 'Aleksander Palamar'
    username: palamar
    password: '$6$bI8p194bQKPJcb4r$bfW73Ff9cDHf4wyEZIhEbz00fGm1fKrD/971QQBnU225gpxifAKmrYafhN/c.aMuJhuHPvHqZO3NYw1oB/PtD/' # Gere um hash seguro, por exemplo, com mkpasswd --method=SHA-512
    hostname: milleniumfalcon
```
- realname, username e password: Configuram o usuário padrão que será criado. A senha deve estar em formato hash (por exemplo, SHA-512).
  - Gere um hash seguro, por exemplo, com `mkpasswd --method=SHA-512`.	
- hostname: Define o nome do host para o sistema instalado.

3. Localidade, Fuso Horário e Layout do Teclado

```yaml
  locale: pt_BR.UTF-8
  timezone: "America/Sao_Paulo"
  keyboard:
    layout: br
```
- locale: Define o idioma e a codificação.
- timezone: Configura o fuso horário.
- keyboard: Determina o layout do teclado.

4. Configuração da Rede

```yaml
  network:
    version: 2
    ethernets:
      enp0s3:
        dhcp4: false
        addresses:
          - 192.168.0.2/24
        gateway4: 192.168.0.1
        nameservers:
          addresses:
            - 1.1.1.3
            - 1.0.0.3
```
- Define uma configuração de rede estática para a interface **enp0s5**. Caso prefira DHCP, basta definir `dhcp4: true` e remover os demais parâmetros.

5. Instalação de Pacotes via apt e snaps

```yaml
  packages:
    - docker.io
    - nodejs
    - npm
    - git
    - curl
    - wget
  
  snaps:
    - name: code
      channel: stable
      classic: true
    - name: slack
      channel: stable
    - name: obsidian
      channel: stable
    - name: postman
      channel: stable
    - name: insomnia
      channel: stable
    - name: discord
      channel: stable
    - name: spotify
      channel: stable
      classic: false
    - name: steam
      channel: stable
```
- packages: Lista de pacotes que serão instalados via apt.
- snaps: Lista de aplicativos a serem instalados via snap. Observe que alguns, como o VSCode (snap "code"), requerem o parâmetro `classic: true`.

6. Instalação de Codecs e Drivers

```yaml
  codecs:
    install: true
  drivers:
    install: true
```
- Estas seções garantem que os pacotes de codecs e drivers de terceiros sejam instalados, oferecendo melhor compatibilidade com multimídia e hardware.

7. Atualizações e política de desligamento

```yaml
  updates: all
  shutdown: reboot
```
- updates: Configura o instalador para aplicar todas as atualizações (segurança e gerais) após a instalação.
- shutdown: Determina que o sistema reinicie ao final do processo de instalação.

### Como Utilizar Este Modelo

1. Personalize o Arquivo:
Edite o arquivo autoinstall.yaml conforme suas necessidades. Você pode modificar o usuário, ajustar a configuração de rede, adicionar ou remover pacotes e snaps, ou incluir comandos customizados (usando as seções `early-commands` e `late-commands`).

2. Fornecendo o Arquivo ao Instalador:
Existem várias formas de utilizar este arquivo durante a instalação:

- Via mídia extraível: Inclua o arquivo no diretório raiz ou na pasta `cidata` de uma mídia USB personalizada.
- Integrado à ISO: Reempacote a ISO do Ubuntu para que o arquivo seja lido automaticamente pelo instalador.
- Via rede (nocloud): Disponibilize o arquivo em um servidor HTTP e indique a URL correspondente na linha de comando do instalador (por exemplo, usando o parâmetro `ds=nocloud-net;s=<URL>`).

3. Testes e Validação:
Antes de aplicar em ambientes de produção, teste sua instalação em uma máquina virtual ou em um ambiente controlado. Utilize logs (em `/var/log/installer/` ou e `/var/log/cloud-init.log`) para diagnosticar possíveis problemas.

### Consulte a documentação official [Ubuntu autoinstall](https://canonical-subiquity.readthedocs-hosted.com/en/latest/reference/autoinstall-reference.html).# autoinstall-ubuntu
