---
title: Solução Azure VMware by CloudSimple - Configurar vCenter na Nuvem Privada para a automação vRealize
description: Descreve como configurar um servidor VMware vCenter na sua CloudSimple Private Cloud como um ponto final para VMware vRealize Automation
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024845"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Configurar vCenter na sua Nuvem Privada para VMware vRealize Automation

Pode configurar um servidor VMware vCenter na sua CloudSimple Private Cloud como ponto final para vMware vRealize Automation.

## <a name="before-you-begin"></a>Antes de começar

Complete estas tarefas antes de configurar o servidor vCenter:

* Configure uma [ligação VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre o seu ambiente no local e a sua Nuvem Privada.
* [Configure o reencaminhamento de DNS no local de pedidos DNS](on-premises-dns-setup.md) para os servidores DNS para a sua Nuvem Privada.
* Envie um pedido de [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para criar um utilizador administrativo vRealize Automation IaaS com o conjunto de permissões listadas na tabela seguinte.

| Valor do Atributo | Permissão |
------------ | ------------- |  
| Arquivo de dados |  Alocar espaço <br> Procurar datastore |
| Datastore Cluster | Configure um Cluster de Datastore |
| Pasta | Criar Pasta <br>Eliminar Pasta |
| Global |  Gerir atributos personalizados<br>Definir Atributo Personalizado |
| Rede | Rede de Atribuição |
| Permissões | Modificar Permissões |
| Recurso | Atribuir VM ao Conjunto de Recursos<br>Migrar alimentado da máquina virtual<br>Migrar alimentado na máquina virtual |
| Inventário de Máquinavirtual |  Criar a partir dos existentes<br>Criar Novo<br>Mover<br>Remover | 
| Interação virtual da máquina |  Configure CD Media<br>Interação com a consola<br>Ligação do dispositivo<br>Desligar<br>Poder ligado<br>Repor<br>Suspender<br>Instalação de ferramentas | 
| Configuração da máquina virtual |  Adicionar disco existente<br>Adicionar novo disco<br>Adicionar ou remover<br>Remover o disco<br>Avançado<br>Alterar a contagem de CPU<br>Alterar Recurso<br>Estender o disco virtual<br>Rastreio de alterações de disco<br>Memória<br>Modificar as definições do dispositivo<br>Mudar o Nome<br>Definir Anotação (versão 5.0 e mais tarde)<br>Definições<br>Colocação de swapfile |
| Aprovisionamento |  Personalizar<br>Modelo de clone<br>Máquina Virtual Clone<br>Implementar Modelo<br>Ler Especificações de Personalização |
| Estado-máquina virtual | Criar instantâneo<br>Remover instantâneo<br>Reverter para Snapshot |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instale a automatização vRealize no seu ambiente no local

1. Inscreva-se no servidor vRealize Automation IaaS como administrador do IaaS que o CloudSimple Support criou para si.
2. Implemente um agente vSphere para o ponto final da automatização vRealize.
    1. Vá para https://*vra-url*:5480/instalador, onde *o vra-url* é o URL que você usa para aceder à vRealize Automation administration UI.
    2. Clique no **Instalador IaaS** para descarregar o instalador.<br>
    A convenção de nomeação para o ficheiro instalador é setup_*vra-url*@5480.exe.
    3. Execute o instalador. No ecrã de Boas-Vindas, clique em **Seguinte**.
    4. Aceite o EULA e clique **em Next**.
    5. Forneça as informações de inscrição, clique em **Aceitar Certificado,** e depois clique em **Seguinte**.
    ![credenciais vRA](media/configure-vra-endpoint-login.png)
    6. Selecione **Agentes de Instalação** e **Procuração** personalizados e clique em **Next**.
    ![vRA tipo de instalação](media/configure-vra-endpoint-install-type.png)
    7. Introduza a informação de entrada do servidor IaaS e clique em **Next**. Se estiver a utilizar o Diretório Ativo, introduza o nome de utilizador no formato **domain\user.** Caso contrário, **user@domain** utilize o formato.
    ![informação de login vRA](media/configure-vra-endpoint-account.png)
    8. Para as definições de procuração, introduza **vSphere** para **o tipo de agente**. Insira um nome para o agente.
    9. Introduza o servidor IaaS FQDN no **Anfitrião** do Serviço de Gerente e nos campos de anfitriões do **Serviço Web Manager.** Clique em **Testar** para testar a ligação para cada um dos valores fQDN. Se o teste falhar, modifique as definições de DNS para que o nome de anfitrião do servidor IaaS seja resolvido.
    10. Introduza um nome para o ponto final do servidor vCenter para a Nuvem Privada. Grave o nome para utilização mais tarde no processo de configuração.

        ![vRA instalar procuração](media/configure-vra-endpoint-proxy.png)

    11. Clique em **Seguinte**.
    12. Clique em **Instalar**.

## <a name="configure-the-vsphere-agent"></a>Configure o agente vSphere

1. Vá a https://*vra-url*/vcac e inscreva-se como **ConfiguraçãoAdmin**.
2. Selecione**pontos finais**de**pontos finais** >  **de infraestrutura** > .
3. Selecione **Nova** > **vSphere****Virtual.** > 
4. Introduza o nome final da vSphere que especificou no procedimento anterior.
5. Para **Endereço**, introduza o URL do servidor vCenter da Nuvem Privada no formato https://*vcenter-fqdn*/sdk, onde *vcenter-fqdn* é o nome do servidor vCenter.
6. Introduza as credenciais para o utilizador administrativo vRealize Automation IaaS que o CloudSimple Support criou para si.
7. Clique em **Ligação de Teste** para validar as credenciais do utilizador. Se o teste falhar, verifique o URL, as informações da conta e o nome e o teste [do ponto final](#verify-the-endpoint-name) novamente.
8. Depois de um teste bem sucedido, clique em **OK** para criar o ponto final da vSphere.
    ![vRA endpoint config acesso](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verifique o nome final

Para identificar o nome final do servidor vCenter correto, faça o seguinte:

1. Abra um pedido de comando no aparelho IaaS.
2. Alterar o diretório para C:\Program Files (x86)\VMware\vCAC\Agents\agent-name, onde o *nome do agente* é o nome que atribuiu ao ponto final do servidor vCenter.
3. Execute o seguinte comando.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

A saída é semelhante à seguinte. O valor `managementEndpointName` do campo é o nome final.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
