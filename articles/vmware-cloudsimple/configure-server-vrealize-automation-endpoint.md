---
title: Azure VMware Solution by CloudSimple - Configurar vCenter em Nuvem Privada para a automação vRealize
description: Descreve como configurar um servidor VMware vCenter na cloudSimple Private Cloud como ponto final para VMware vRealize Automation
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b6c6a320e6299808a91214476c8c0460f9f53d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895058"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Configurar o vCenter na sua Nuvem Privada para VMware vRealize Automation

Pode configurar um servidor VMware vCenter na cloudSimple Private Cloud como ponto final para VMware vRealize Automation.

## <a name="before-you-begin"></a>Antes de começar

Complete estas tarefas antes de configurar o servidor vCenter:

* Configure uma [ligação VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre o seu ambiente no local e a sua Nuvem Privada.
* [Configurar o encaminhamento de DNS de pedidos DNS no local](on-premises-dns-setup.md) para os servidores DNS para a sua Nuvem Privada.
* Envie um pedido de [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para criar um utilizador administrativo vRealize Automation IaAS com o conjunto de permissões que estão listadas na tabela seguinte.

| Valor do Atributo | Permissão |
------------ | ------------- |  
| Arquivo de dados |  Alocar Espaço <br> Procurar datastore |
| Datastore Cluster | Configurar um cluster de datastore |
| Pasta | Criar Pasta <br>Eliminar Pasta |
| Global |  Gerir atributos personalizados<br>Definir Atributo Personalizado |
| Rede | Rede de Atribuição |
| Permissões | Modificar permissões |
| Recurso | Atribuir VM ao Pool de Recursos<br>Migrar Máquina Virtual Desligada<br>Migrar alimentado em máquina virtual |
| Inventário de máquinas virtuais |  Criar a partir de existente<br>Criar Novo<br>Mover<br>Remover | 
| Interação de máquinas virtuais |  Configure CD Media<br>Interação com consolas<br>Ligação do dispositivo<br>Desligar<br>Ligar<br>Repor<br>Suspender<br>Instalação de ferramentas | 
| Configuração de máquina virtual |  Adicionar disco existente<br>Adicionar novo disco<br>Adicionar ou remover<br>Remover Disco<br>Avançado<br>Alterar contagem de CPU<br>Alterar recurso<br>Estender o disco virtual<br>Rastreio de mudança de disco<br>Memória<br>Modificar as definições do dispositivo<br>Mudar o Nome<br>Anotação definida (versão 5.0 e posterior)<br>Definições<br>Colocação de ficheiros de permuta |
| Aprovisionamento |  Personalizar<br>Modelo de clone<br>Máquina virtual clone<br>Implementar Modelo<br>Ler Especificações de Personalização |
| Estado da Máquina Virtual | Criar Instantâneo<br>Remover instantâneo<br>Reverter para Snapshot |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instale a vRealize Automation no seu ambiente no local

1. Inscreva-se no servidor vRealize Automation IaaS como administrador iaaS que o Suporte CloudSimple criou para si.
2. Implementar um agente vSphere para o ponto final vRealize Automation.
    1. Vá a https:// url vra:5480/instalador, onde *vra-url* é o URL que você usa para aceder à uI administração vRealize Automation.
    2. Clique no **Instalador IaaS** para descarregar o instalador.<br>
    A convenção de nomeação para o ficheiro do instalador é setup_ *vra-url* @5480.exe .
    3. Execute o instalador. No ecrã de Boas-Vindas, clique em **Seguinte**.
    4. Aceite o EULA e clique em **Seguinte**.
    5. Forneça a informação de inscrição, clique em **Aceitar Certificado** e, em seguida, clique em **Seguinte**.
    ![credenciais vRA](media/configure-vra-endpoint-login.png)
    6. Selecione **Agentes de Instalação** e **Procuração Personalizados** e clique em **Seguinte**.
    ![vRA tipo de instalação](media/configure-vra-endpoint-install-type.png)
    7. Introduza as informações de inscrição do servidor IaaS e clique em **Seguinte**. Se estiver a utilizar o Ative Directory, insira o nome de utilizador no formato **domínio\user.** Caso contrário, utilize **user@domain** o formato.
    ![informação de login vRA](media/configure-vra-endpoint-account.png)
    8. Para as definições de procuração, **introduza vSphere** para **o tipo agente**. Insira um nome para o agente.
    9. Insira o servidor IaaS FQDN nos **campos De serviço de gerente** e de anfitrião de serviços web do Gestor de **Modelos.** Clique **em Testar** para testar a ligação para cada um dos valores FQDN. Se o teste falhar, modifique as definições de DNS de modo a que o nome de anfitrião do servidor IaaS seja resolvido.
    10. Introduza um nome para o ponto final do servidor vCenter para a Nuvem Privada. Grave o nome para utilização mais tarde no processo de configuração.

        ![vRA instalar proxy](media/configure-vra-endpoint-proxy.png)

    11. Clique em **Seguinte**.
    12. Clique em **Install** (Instalar).

## <a name="configure-the-vsphere-agent"></a>Configure o agente vSphere

1. Vá a https://*vra-url*/vcac e inscreva-se como **ConfigurationAdmin**.
2. **Selecione**  >  **Pontos finais de**  >  **pontos finais de infraestrutura**.
3. Selecione   >  **New Virtual**  >  **vSphere**.
4. Introduza o nome do ponto final vSphere especificado no procedimento anterior.
5. Para **endereço**, insira o URL do servidor vCenter private cloud no formato https://*vcenter-fqdn*/sdk, onde *vcenter-fqdn* é o nome do servidor vCenter.
6. Introduza as credenciais para o utilizador administrativo vRealize Automation IaaS que o CloudSimple Support criou para si.
7. Clique em 'Ligar o **Teste'** para validar as credenciais do utilizador. Se o teste falhar, verifique o URL, as informações da conta e [o nome do ponto final](#verify-the-endpoint-name) e volte a testar.
8. Depois de um teste bem sucedido, clique **em OK** para criar o ponto final vSphere.
    ![vRA endpoint config acesso](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verifique o nome do ponto final

Para identificar o nome correto do servidor vCenter, faça o seguinte:

1. Abra um aviso de comando no aparelho IaaS.
2. Altere o diretório para C:\Program Files (x86)\VMware\vCAC\Agents\agent-name, onde o *nome do agente* é o nome atribuído ao ponto final do servidor vCenter.
3. Execute o seguinte comando.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

A saída é semelhante à seguinte. O valor do `managementEndpointName` campo é o nome do ponto final.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
