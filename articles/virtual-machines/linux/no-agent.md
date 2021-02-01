---
title: Criar imagens Linux sem um agente de provisionamento
description: Crie imagens Linux generalizadas sem um agente de provisionamento em Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 1c9ac872587804adbd9e62a3dc3ef3daed9e0c25
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223056"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Criação de imagens generalizadas sem um agente de provisionamento

O Microsoft Azure fornece agentes de provisão para Os VMs Linux sob a forma do [walinuxagent](https://github.com/Azure/WALinuxAgent) ou [cloud-init](https://github.com/canonical/cloud-init) (recomendado). Mas pode haver um cenário em que não queira usar nenhuma destas aplicações para o seu agente de provisionamento, tais como:

- O seu distro/versão Linux não suporta o agente cloud-init/Linux.
- Você precisa de propriedades VM específicas para ser definida, como o nome de hospedeiro.

> [!NOTE] 
>
> Se não necessitar de que sejam definidas quaisquer propriedades ou qualquer forma de provisionamento, deve considerar a criação de uma imagem especializada.

Este artigo mostra como pode configurar a sua imagem VM para satisfazer os requisitos da plataforma Azure e definir o nome de anfitrião, sem instalar um agente de provisionamento.

## <a name="networking-and-reporting-ready"></a>Rede e reportagem prontas

Para que o seu Linux VM comunique com os componentes Azure, necessitará que um cliente DHCP recupere um IP de anfitrião da rede virtual, bem como a resolução de DNS e gestão de rotas. A maioria desativa a nave com estes utilitários fora da caixa. As ferramentas que foram testadas no Azure por fornecedores de distro Linux incluem `dhclient` `network-manager` , e `systemd-networkd` outros.

> [!NOTE]
>
> Atualmente, a criação de imagens generalizadas sem um agente de provisionamento suporta apenas VMs ativados por DHCP.

Depois de configurar e configurar a ligação em rede, tem de "reportar pronto". Isto dirá ao Azure que o VM tem vindo a provisões com sucesso.

> [!IMPORTANT]
>
> Se não se apresentar pronto para o Azure, o seu VM será reiniciado!

## <a name="demosample"></a>Demonstração/amostra

Esta demonstração mostrará como pode tirar uma imagem de Marketplace existente (neste caso, um Debian Buster VM) e remover o Agente Linux (walinuxagent), mas também criar o processo mais básico para informar ao Azure que o VM está "pronto".

### <a name="create-the-resource-group-and-base-vm"></a>Criar o grupo de recursos e o VM base:

```bash
$ az group create --location eastus --name demo1
```

Criar o VM base:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Remova o agente de provisionamento de imagem

Uma vez que o VM está a forragem, você pode SSH nele e remover o agente Linux:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Adicionar código obrigatório ao VM

Também dentro do VM, porque removemos o Agente Azure Linux, precisamos fornecer um mecanismo para reportar pronto. 

#### <a name="python-script"></a>Script de Python

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Passos genéricos (sem usar Python)

Se o seu VM não tiver Python instalado ou disponível, pode reproduzir programáticamente esta lógica de script acima com os seguintes passos:

1. Recupere a `ContainerId` resposta e `InstanceId` analisando a resposta do WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Construa os seguintes dados XML, injetando a análise `ContainerId` e a partir do passo `InstanceId` acima:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Publique estes dados na WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatização executando o código na primeira bota

Esta demo usa sistema, que é o sistema init mais comum nos modernos distros Linux. Assim, a forma mais fácil e nativa de garantir que este mecanismo de relatório é executado no momento certo é criar uma unidade de serviço sistema. Pode adicionar o seguinte ficheiro de unidade para `/etc/systemd/system` (este exemplo designa o ficheiro da `azure-provisioning.service` unidade):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Este serviço sistemado faz três coisas para o fornecimento básico:

1. Relatórios prontos para Azure (para indicar que surgiu com sucesso).
1. Rebatiza o VM com base no nome VM fornecido pelo utilizador, retirando estes dados do [Azure Instance Medata Service (IMDS)](./instance-metadata-service.md). **Nota** O IMDS também fornece outros [metadados de exemplo,](./instance-metadata-service.md#access-azure-instance-metadata-service)como as Chaves Públicas SSH, para que possa definir mais do que o nome de anfitrião.
1. Desativa-se por si só para que corra apenas na primeira bota e não em reboots subsequentes.

Com a unidade no sistema de ficheiros, execute o seguinte para o ativar:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Agora o VM está pronto para ser generalizado e ter uma imagem criada a partir dele. 

#### <a name="completing-the-preparation-of-the-image"></a>Completar a preparação da imagem

De volta à sua máquina de desenvolvimento, corra o seguinte para preparar a criação de imagem a partir da base VM:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

E criar a imagem a partir deste VM:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Agora estamos prontos para criar um novo VM (ou múltiplos VMs) a partir da imagem:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> É importante definir `--enable-agent` `false` para porque walinuxagent não existe neste VM que vai ser criado a partir da imagem.

Este VM deve provisição com sucesso. Iniciar sessão no VM recém-aderado, deverá ser possível ver a saída do serviço sistema pronto para o relatório:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Suporte

Se implementar o seu próprio código/agente de provisionamento, então é dono do suporte deste código, o suporte da Microsoft apenas investigará questões relacionadas com as interfaces de provisionamento que não estão disponíveis. Estamos continuamente a fazer melhorias e alterações nesta área, pelo que deve monitorizar as alterações no cloud-init e no Agente Azure Linux para o fornecimento de alterações na API.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [o fornecimento de Linux.](provisioning.md)
