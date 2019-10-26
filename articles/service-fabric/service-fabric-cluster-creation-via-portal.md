---
title: Criar um Cluster Service Fabric no portal do Azure | Microsoft Docs
description: Saiba como configurar um cluster de Service Fabric seguro no Azure usando o portal do Azure e Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: atsenthi
ms.openlocfilehash: ca050e19ed37e2895507507338ebf4b4217851b6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934045"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um Cluster Service Fabric no Azure usando o portal do Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que orienta você pelas etapas de configuração de um Cluster Service Fabric (Linux ou Windows) no Azure usando o portal do Azure. Este guia orienta você pelas seguintes etapas:

* Crie um cluster no Azure por meio do portal do Azure.
* Autentique os administradores usando certificados.

> [!NOTE]
> Para obter opções de segurança mais avançadas, como autenticação de usuário com Azure Active Directory e configurar certificados para segurança de aplicativo, [crie seu cluster usando Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Segurança em clusters 
Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [Service Fabric cluster security scenarios (Cenários de segurança do cluster do Service Fabric)][service-fabric-cluster-security].

Se esta for a primeira vez que você está criando um cluster do Service Fabric ou implantando um cluster para cargas de trabalho de teste, você pode pular para a próxima seção (**criar cluster no portal do Azure**) e fazer com que o sistema gere certificados necessários para seus clusters que executam cargas de trabalho de teste. Se você estiver configurando um cluster para cargas de trabalho de produção, continue lendo.

#### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (obrigatório)
Esse certificado é necessário para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de duas maneiras:

* **Autenticação de cluster:** Autentica a comunicação de nó para nó para a Federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster.
* **Autenticação do servidor:** Autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o cliente de gerenciamento saiba que ele está se comunicando com o cluster real. Esse certificado também fornece SSL para a API de gerenciamento de HTTPS e para Service Fabric Explorer por HTTPS.

Para atender a essas finalidades, o certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo de troca de informações pessoais (. pfx).
* O nome da **entidade do certificado deve corresponder ao domínio** usado para acessar o cluster de Service Fabric. Isso é necessário para fornecer SSL para os pontos de extremidade de gerenciamento HTTPS do cluster e Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (CA) para o domínio `.cloudapp.azure.com`. Adquira um nome de domínio personalizado para o cluster. Quando você solicita um certificado de uma autoridade de certificação, o nome da entidade do certificado deve corresponder ao nome de domínio personalizado usado para o cluster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticação de cliente
Certificados de cliente adicionais autenticam administradores para tarefas de gerenciamento de cluster. Service Fabric tem dois níveis de acesso: **administrador** e **usuário somente leitura**. No mínimo, um único certificado para acesso administrativo deve ser usado. Para obter acesso adicional no nível do usuário, um certificado separado deve ser fornecido. Para obter mais informações sobre funções de acesso, consulte [controle de acesso baseado em função para clientes Service Fabric][service-fabric-cluster-security-roles].

Você não precisa carregar certificados de autenticação de cliente para Key Vault para trabalhar com Service Fabric. Esses certificados só precisam ser fornecidos a usuários que estão autorizados para gerenciamento de cluster. 

> [!NOTE]
> Azure Active Directory é a maneira recomendada para autenticar clientes para operações de gerenciamento de cluster. Para usar Azure Active Directory, você deve [criar um cluster usando Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcional)
Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança de aplicativo. Antes de criar o cluster, considere os cenários de segurança do aplicativo que exigem a instalação de um certificado nos nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo
* Criptografia de dados entre nós durante a replicação 

Os certificados de aplicativo não podem ser configurados durante [a criação de um cluster por meio do portal do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Para configurar certificados de aplicativo no momento da instalação do cluster, você deve [criar um cluster usando Azure Resource Manager][create-cluster-arm]. Você também pode adicionar certificados de aplicativo ao cluster depois que ele tiver sido criado.

## <a name="create-cluster-in-the-azure-portal"></a>Criar cluster no portal do Azure

A criação de um cluster de produção para atender às necessidades do seu aplicativo envolve algum planejamento, para ajudá-lo com isso, é altamente recomendável que você leia e entenda o documento [Service Fabric considerações sobre planejamento de cluster][service-fabric-cluster-capacity] . 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Pesquisar o recurso de Cluster Service Fabric

Inicie sessão no [portal do Azure][azure-portal].
Clique em **criar um recurso** para adicionar um novo modelo de recurso. Pesquise o modelo de Cluster Service Fabric no **Marketplace** em **tudo**.
Selecione **Service Fabric cluster** na lista.

![Procure Service Fabric modelo de cluster no portal do Azure.][SearchforServiceFabricClusterTemplate]

Navegue até a folha **Service Fabric cluster** e clique em **criar**.

A folha **criar Service Fabric cluster** tem as quatro etapas a seguir:

### <a name="1-basics"></a>1. noções básicas
![Captura de tela da criação de um novo grupo de recursos.][CreateRG]

Na folha noções básicas, você precisa fornecer os detalhes básicos para o cluster.

1. Insira o nome do cluster.
2. Insira um **nome de usuário** e uma **senha** para área de trabalho remota para as VMs.
3. Certifique-se de selecionar a **assinatura** na qual deseja que o cluster seja implantado, especialmente se você tiver várias assinaturas.
4. Crie um novo **grupo de recursos**. É melhor dar a ele o mesmo nome que o cluster, pois ele ajuda a encontrá-lo mais tarde, especialmente quando você está tentando fazer alterações na implantação ou excluir o cluster.
   
   > [!NOTE]
   > Embora você possa optar por usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isso facilita a exclusão de clusters e de todos os recursos que ele usa.
   > 
   > 
5. Selecione o **local** no qual você deseja criar o cluster. Se você estiver planejando usar um certificado existente que você já carregou em um cofre de chaves, deverá usar a mesma região em que o cofre de chaves está. 

### <a name="2-cluster-configuration"></a>2. configuração de cluster
![Criar um tipo de nó][CreateNodeType]

Configure seus nós de cluster. Os tipos de nó definem os tamanhos de VM, o número de VMs e suas propriedades. O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no Portal) deve ter pelo menos cinco VMs, pois esse é o tipo de nó em que Service Fabric serviços do sistema são colocados. Não configure **as propriedades de posicionamento** porque uma propriedade de posicionamento padrão de "nodetypename" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nó é um aplicativo que contém um serviço de front-end e um serviço de back-end. Você deseja colocar o serviço de front-end em VMs menores (tamanhos de VM, como D2_V2) com portas abertas para a Internet e colocar o serviço de back-end em VMs maiores (com tamanhos de VM, como D3_V2, D6_V2, D15_V2 e assim por diante) sem nenhuma porta voltada para a Internet aberta.
> 

1. Escolha um nome para o tipo de nó (de 1 a 12 caracteres contendo apenas letras e números).
2. O **tamanho** mínimo das VMs para o tipo de nó primário é controlado pela **camada de durabilidade** que você escolhe para o cluster. O padrão para a camada de durabilidade é bronze. Para obter mais informações sobre durabilidade, consulte [como escolher a durabilidade do cluster Service Fabric][service-fabric-cluster-durability].
3. Selecione o **tamanho da máquina virtual**. As VMs da série D têm unidades SSD e são altamente recomendadas para aplicativos com estado. Não use nenhuma SKU de VM que tenha núcleos parciais ou tenha menos de 10 GB de capacidade de disco disponível. Consulte o [documento de considerações sobre planejamento de cluster do Service Fabric][service-fabric-cluster-capacity] para obter ajuda na seleção do tamanho da VM.
4.  O **cluster de nó único e os clusters de três nós** destinam-se somente ao uso de teste. Eles não têm suporte para cargas de trabalho de produção em execução.
5. Escolha a **capacidade inicial do conjunto de dimensionamento de VM** para o tipo de nó. Você pode escalar ou reduzir verticalmente o número de VMs em um tipo de nó posteriormente, mas no tipo de nó primário, o mínimo é cinco para cargas de trabalho de produção. Outros tipos de nó podem ter, no mínimo, uma VM. O **número** mínimo de VMs para o tipo de nó primário impulsiona a **confiabilidade** do cluster.  
6. Configurar **pontos de extremidade personalizados**. Esse campo permite que você insira uma lista separada por vírgulas de portas que você deseja expor por meio do Azure Load Balancer à Internet pública para seus aplicativos. Por exemplo, se você planeja implantar um aplicativo Web em seu cluster, digite "80" aqui para permitir o tráfego na porta 80 para o cluster. Para obter mais informações sobre pontos de extremidade, consulte [comunicando-se com aplicativos][service-fabric-connect-and-communicate-with-services]
7. **Habilite o proxy reverso**.  O [Service Fabric proxy reverso](service-fabric-reverseproxy.md) ajuda os microserviços em execução em uma descoberta de Cluster Service Fabric e se comunicam com outros serviços que têm pontos de extremidade http.
8. De volta à folha **configuração de cluster** , em **+ Mostrar configurações opcionais**, configure o **diagnóstico**de cluster. Por padrão, os diagnósticos são habilitados no cluster para auxiliar na solução de problemas. Se você quiser desabilitar o diagnóstico, altere o **status** alternar para **desativado**. **Não** é recomendável desativar o diagnóstico. Se você já tiver Application Insights projeto criado, forneça sua chave para que os rastreamentos do aplicativo sejam roteados para ele.
9. **Incluir serviço DNS**.  O [serviço DNS](service-fabric-dnsservice.md) é um serviço opcional que permite que você encontre outros serviços usando o protocolo DNS.
10. Selecione o **modo de atualização do Fabric** para o qual você deseja definir o cluster. Selecione **automático**, se desejar que o sistema escolha automaticamente a versão mais recente disponível e tente atualizar o cluster para ele. Defina o modo como **manual**, se desejar escolher uma versão com suporte. Para obter mais detalhes sobre o modo de atualização do fabric, consulte o [documento de atualização do Cluster Service Fabric.][service-fabric-cluster-upgrade]

> [!NOTE]
> Damos suporte apenas a clusters que executam versões com suporte do Service Fabric. Ao selecionar o modo **manual** , você está assumindo a responsabilidade de atualizar o cluster para uma versão com suporte.
> 

### <a name="3-security"></a>3. segurança
![Captura de tela das configurações de segurança no portal do Azure.][BasicSecurityConfigs]

Para facilitar a configuração de um cluster de teste seguro para você, fornecemos a opção **básica** . Se você já tiver um certificado e o carregou no cofre de [chaves](/azure/key-vault/) (e tiver habilitado o cofre de chaves para implantação), use a opção **personalizado**

#### <a name="basic-option"></a>Opção básica
Siga as telas para adicionar ou reutilizar um cofre de chaves existente e adicionar um certificado. A adição do certificado é um processo síncrono e, portanto, você precisará aguardar até que o certificado seja criado.

Resista à tentação de navegar para fora da tela até que o processo anterior seja concluído.

![CreateKeyVault]

Agora que o cofre de chaves foi criado, edite as políticas de acesso para o cofre de chaves. 

![CreateKeyVault2]

Clique em **Editar políticas de acesso**, em seguida, **Mostrar políticas de acesso avançadas** e habilitar o acesso às máquinas virtuais do Azure para implantação. É recomendável habilitar também a implantação do modelo. Depois de fazer suas seleções, não se esqueça de clicar no botão **salvar** e fechar o painel **políticas de acesso** .

![CreateKeyVault3]

Insira o nome do certificado e clique em **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Opção personalizada
Ignore esta seção se você já tiver executado as etapas na opção **básica** .

![SecurityCustomOption]

Você precisa do cofre de chaves de origem, da URL do certificado e das informações de impressão digital do certificado para concluir a página de segurança. Se você não o tiver à mão, abra outra janela do navegador e, no portal do Azure, faça o seguinte

1. Navegue até o serviço do cofre de chaves.
2. Selecione a guia "Propriedades" e copie o "ID do recurso" para "cofre de chaves de origem" na janela de outro navegador 

    ![CertInfo0]

3. Agora, selecione a guia "certificados".
4. Clique em impressão digital do certificado, que leva você para a página versões.
5. Clique nos GUIDs que você vê na versão atual.

    ![CertInfo1]

6. Agora você deve estar na tela como abaixo. Copiar a impressão digital de SHA-1 hexadecimal para "impressão digital do certificado" na outra janela do navegador
7. Copie o ' identificador secreto ' para a "URL do certificado" em outra janela do navegador.

    ![CertInfo2]

Marque a caixa **definir configurações avançadas** para inserir certificados de cliente para **cliente administrador** e **cliente somente leitura**. Nesses campos, insira a impressão digital do seu certificado de cliente de administrador e a impressão digital do seu certificado de cliente de usuário somente leitura, se aplicável. Quando os administradores tentam se conectar ao cluster, eles recebem acesso somente se tiverem um certificado com uma impressão digital que corresponda aos valores de impressão digital inseridos aqui.  

### <a name="4-summary"></a>4. Resumo

Agora você está pronto para implantar o cluster. Antes de fazer isso, baixe o certificado, procure dentro da caixa informativa azul grande para o link. Certifique-se de manter o certificado em um local seguro. Você precisa dela para se conectar ao cluster. Como o certificado que você baixou não tem uma senha, é recomendável que você adicione um.

Para concluir a criação do cluster, clique em **criar**. Opcionalmente, você pode baixar o modelo.

![Resumo]

Pode ver o progresso da criação nas notificações. (Clique no ícone "Bell" próximo à barra de status na parte superior direita da tela.) Se você clicou **em fixar no quadro inicial** ao criar o cluster, você vê **implantando Service Fabric cluster** fixado no quadro **inicial** . Esse processo levará algum tempo. 

Para executar operações de gerenciamento em seu cluster usando o PowerShell ou a CLI, você precisa se conectar ao cluster e ler mais sobre como [conectar-se ao cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Exibir o status do cluster
![Captura de tela dos detalhes do cluster no painel.][ClusterDashboard]

Depois que o cluster for criado, você poderá inspecionar o cluster no Portal:

1. Vá para **procurar** e clique em **Service Fabric clusters**.
2. Localize o cluster e clique nele.
3. Agora pode ver os detalhes do seu cluster no dashboard, incluindo o ponto final público do cluster e uma ligação para o Service Fabric Explorer.

A seção **Monitor do nó** na folha do painel do cluster indica o número de VMs que estão íntegras e não estão íntegras. Você pode encontrar mais detalhes sobre a integridade do cluster em [Service Fabric introdução ao modelo de integridade][service-fabric-health-introduction].

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós para sempre manter a disponibilidade e preservar o estado conhecido como "manutenção de quorum". Portanto, normalmente não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do seu estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do conjunto de dimensionamento de máquinas virtuais ou a um nó de cluster
Cada um dos NodeTypes que você especifica em seu cluster resulta em um conjunto de dimensionamento de máquinas virtuais que está sendo configurado. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Passos seguintes
Neste ponto, você tem um cluster seguro usando certificados para autenticação de gerenciamento. Em seguida, [Conecte-se ao seu cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar os segredos do aplicativo](service-fabric-application-secret-management.md).  Além disso, saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Resumo]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
