---
title: Criar um cluster do Service Fabric no portal do Azure | Documentos da Microsoft
description: Saiba como configurar um cluster do Service Fabric seguro no Azure com o portal do Azure e o Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: 80a4014cb371f3c37c01fa1cf2d20041caf17184
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448226"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um cluster do Service Fabric no Azure com o portal do Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que o orienta pelas etapas de configuração de um cluster do Service Fabric (Linux ou Windows) no Azure com o portal do Azure. Este guia explica os passos seguintes:

* Crie um cluster no Azure através do portal do Azure.
* Autenticar os administradores a utilização de certificados.

> [!NOTE]
> Para opções de segurança mais avançadas, como a autenticação de utilizador com o Azure Active Directory e como configurar certificados para segurança de aplicação, [criar o cluster com o Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Segurança do cluster 
Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, veja [cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security].

Se esta for a primeira vez que está a criar um cluster do service fabric ou estiver a implementar um cluster para cargas de trabalho de teste, pode avançar para a secção seguinte (**criar um cluster no Portal do Azure**) e o sistema gerar os certificados necessários para os clusters que executam cargas de trabalho de teste. Se estiver a configurar um cluster para cargas de trabalho de produção, em seguida, continue a ler.

#### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e servidor (obrigatório)
Este certificado é necessário para proteger um cluster e evitar o acesso não autorizado ao mesmo. Ele fornece a segurança do cluster de duas formas:

* **Autenticação do cluster:** Efetua a autenticação de comunicação de nó para nó para a Federação de cluster. Apenas nós que podem provar a sua identidade com este certificado podem associar o cluster.
* **Autenticação de servidor:** Efetua a autenticação de pontos finais de gestão de cluster para um cliente de gestão, para que o cliente de gestão saiba que ele está falando ao real cluster. Este certificado fornece também SSL para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS.

Para os seguintes objetivos, o certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para troca de chaves, exportável para um ficheiro Personal Information Exchange (. pfx).
* O certificado **nome do requerente tem de corresponder ao domínio** utilizado para aceder ao cluster do Service Fabric. Isto é necessário para fornecer o SSL para pontos finais de gestão HTTPS do cluster e o Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o `.cloudapp.azure.com` domínio. Adquira um nome de domínio personalizado para o seu cluster. Quando pedir um certificado a partir de uma AC nome do requerente do certificado tem de corresponder ao nome de domínio personalizado utilizado para o seu cluster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticação de cliente
Certificados de cliente adicionais autenticar os administradores para tarefas de gestão do cluster. Service Fabric tem dois níveis de acesso: **administrador** e **utilizador só de leitura**. No mínimo, deve ser utilizado um único certificado para acesso administrativo. Para acesso de nível de usuário adicional, deve ser fornecido um certificado diferente. Para obter mais informações sobre as funções de acesso, consulte [controlo de acesso baseado em funções para clientes do Service Fabric][service-fabric-cluster-security-roles].

Não é necessário carregar certificados de autenticação de cliente para o Cofre de chaves para trabalhar com o Service Fabric. Estes certificados só tem de ser fornecido aos usuários que estão autorizados a gestão de clusters. 

> [!NOTE]
> O Azure Active Directory é a forma recomendada para autenticar clientes para operações de gestão do cluster. Para utilizar o Azure Active Directory, terá [criar um cluster com o Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de aplicação (opcionais)
Qualquer número de certificados adicionais pode ser instalado num cluster por motivos de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicações que necessitam de um certificado para ser instalado em nós, tal como:

* Encriptação e desencriptação de valores de configuração de aplicações
* Encriptação de dados entre os nós durante a replicação 

Certificados de aplicação não podem ser configurado quando [criar um cluster através do portal do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Para configurar certificados de aplicação no momento da configuração de cluster, deve [criar um cluster com o Azure Resource Manager][create-cluster-arm]. Também pode adicionar certificados de aplicação para o cluster depois de este ter sido criado.

## <a name="create-cluster-in-the-azure-portal"></a>Criar o cluster no portal do Azure

Criar um cluster de produção para atender às necessidades da sua aplicação envolve algum planejamento, para ajudar com isso, recomenda-se vivamente que leia e compreenda o [considerações de planeamento de Cluster do Service Fabric] [ service-fabric-cluster-capacity] documento. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Procure o recurso de cluster do Service Fabric

Inicie sessão no [Portal do Azure][azure-portal].
Clique em **criar um recurso** para adicionar um novo modelo de recurso. Pesquisa para o modelo de Cluster do Service Fabric na **Marketplace** sob **tudo**.
Selecione **Cluster do Service Fabric** da lista.

![Procure o modelo de cluster do Service Fabric no portal do Azure.][SearchforServiceFabricClusterTemplate]

Navegue para o **Cluster do Service Fabric** painel e clique em **criar**.

O **cluster do Service Fabric criar** painel tem os seguintes quatro passos:

### <a name="1-basics"></a>1. Noções básicas
![Captura de ecrã da criação de um novo grupo de recursos.][CreateRG]

No painel Noções básicas, terá de fornecer os detalhes básicos para o seu cluster.

1. Introduza o nome do cluster.
2. Introduza um **nome de utilizador** e **palavra-passe** para ambiente de trabalho remoto para as VMs.
3. Certifique-se de selecionar o **subscrição** que pretende que o cluster devem ser implantados, especialmente se tiver várias subscrições.
4. Criar uma nova **grupo de recursos**. É melhor dar a ele o mesmo nome que o cluster, uma vez que o ajuda a encontrar mais tarde, especialmente quando está tentando fazer alterações à sua implementação ou eliminar o cluster.
   
   > [!NOTE]
   > Embora pode optar por utilizar um grupo de recursos existente, é uma boa prática para criar um novo grupo de recursos. Isto torna mais fácil eliminar clusters e todos os recursos de que utiliza.
   > 
   > 
5. Selecione o **localização** no qual pretende criar o cluster. Se estiver a planear utilizar um certificado existente que já tenha carregado para um cofre de chaves, tem de utilizar a mesma região que o Cofre de chaves está na. 

### <a name="2-cluster-configuration"></a>2. Configuração de cluster
![Criar um tipo de nó][CreateNodeType]

Configure os nós do cluster. Tipos de nó definem os tamanhos VM, o número de VMs e as respetivas propriedades. O cluster pode ter mais de um tipo de nó, mas o tipo de nó principal (aquele primeiro que definir no portal) tem de ter, pelo menos, cinco VMs, como este é o tipo de nó em que os serviços de sistema do Service Fabric são colocados. Não configure **as propriedades de colocação** porque uma propriedade de posicionamento de padrão de "NodeTypeName" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nó é uma aplicação que contém um serviço de front-end e um serviço de back-end. Que pretende colocar o serviço de front-end em VMs menores (tamanhos de VM, como D2_V2) com portas abertas para a Internet e colocar o serviço de back-end em VMs maiores (com tamanhos de VM, como D3_V2, D6_V2, D15_V2 e assim por diante) com nenhuma porta de acesso à Internet aberta.
> 

1. Escolha um nome para o seu tipo de nó (1 e 12 carateres contendo apenas letras e números).
2. O mínimo **tamanho** de VMs para o nó primário, tipo é orientado pela **escalão de durabilidade** que escolher para o cluster. A predefinição para o escalão de durabilidade é bronze. Para obter mais informações sobre a durabilidade, veja [como escolher a durabilidade de cluster do Service Fabric][service-fabric-cluster-durability].
3. Selecione o **tamanho da Máquina Virtual**. VMs de série D possuem unidades SSD e são altamente recomendadas para aplicações com monitorização de estado. Não utilizar qualquer SKU de VM que tenha núcleos parciais ou de ter menos de 10 GB de capacidade de disco disponível. Consulte a [documento de considerações de planeamento de cluster do service fabric] [ service-fabric-cluster-capacity] para obter ajuda na seleção do tamanho VM.
4.  **Único cluster de nó e três clusters de nós** destinam-se apenas a utilização de teste. Não são suportadas para quaisquer cargas de trabalho de produção em execução.
5. Escolha o **capacidade do conjunto de dimensionamento de VM inicial** para o tipo de nó. Pode aumentar ou reduzir verticalmente o número de VMs num tipo de nó mais tarde, mas o tipo de nó primário, o mínimo é cinco para cargas de trabalho de produção. Outros tipos de nó podem ter um mínimo de uma VM. O mínimo **número** de VMs para as unidades do tipo de nó principal do **confiabilidade** do seu cluster.  
6. Configurar **pontos finais personalizados**. Este campo permite-lhe introduzir uma lista separada por vírgulas de portas que pretende expor através do Balanceador de carga do Azure para a Internet pública para as suas aplicações. Por exemplo, se planeia implementar uma aplicação web no seu cluster, introduza "80" aqui para permitir o tráfego na porta 80 no seu cluster. Para obter mais informações sobre pontos finais, consulte [ao comunicar com aplicações][service-fabric-connect-and-communicate-with-services]
7. **Ativar proxy inverso**.  O [proxy inverso do Service Fabric](service-fabric-reverseproxy.md) microsserviços ajuda a executar num cluster do Service Fabric detetar e comunicar com outros serviços que têm pontos finais de http.
8. De volta a **configuração do Cluster** painel, em **+ Mostrar definições opcionais**, configurar o cluster **diagnóstico**. Por predefinição, os diagnósticos estão ativados no seu cluster para ajudar na resolução de problemas. Se pretender desativar a alteração de diagnóstico a **Status** alternar para **desativar**. Desativação do diagnóstico está **não** recomendado. Se já tiver o projeto do Application Insights criado, então dê a sua chave, para que os rastreios de aplicações são encaminhados para o mesmo.
9. **Incluir serviço DNS**.  O [serviço DNS](service-fabric-dnsservice.md) um serviço opcional que permite-lhe encontrar outros serviços que utilizam o protocolo DNS.
10. Selecione o **modo de atualização do Fabric** quiser definir o seu cluster como. Selecione **automática**, se pretender que o sistema para escolher a versão mais recente disponível e tente atualizar o seu cluster para o mesmo automaticamente. Definir o modo como **Manual**, se pretender escolher uma versão suportada. Para obter mais detalhes sobre os recursos de infraestrutura atualizar consulte de modo a [documento de atualização de Cluster do Service Fabric.][service-fabric-cluster-upgrade]

> [!NOTE]
> Damos suporte a apenas os clusters que executem versões suportadas do Service Fabric. Ao selecionar o **Manual** modo, que está a efetuar a responsabilidade para atualizar o cluster para uma versão suportada.
> 

### <a name="3-security"></a>3. Segurança
![Captura de ecrã das configurações de segurança no portal do Azure.][BasicSecurityConfigs]

Para facilitar como configurar um cluster de teste seguro para, nós fornecemos os **básica** opção. Se já tiver um certificado e carregou para sua [Cofre de chaves](/azure/key-vault/) (e ativado o Cofre de chaves para a implementação), em seguida, utilize o **personalizado** opção

#### <a name="basic-option"></a>Opção básico
Siga os ecrãs para adicionar ou reutilizar o Cofre de chaves existente e adicionar um certificado. A adição do certificado é um processo síncrono e por isso, terá que aguardar que o certificado a ser criada.

Resista à tentação de navegar para fora do ecrã até que o processo anterior seja concluído.

![CreateKeyVault]

Agora que o Cofre de chaves é criado, edite as políticas de acesso para o seu Cofre de chaves. 

![CreateKeyVault2]

Clique no **políticas de acesso de edição**, em seguida, **Mostrar avançadas de políticas de acesso** e ativar o acesso às máquinas de virtuais do Azure para a implementação. Recomenda-se que ative a implementação do modelo também. Depois de efetuar as seleções, não se esqueça de clicar o **salvar** botão e fechar da **políticas de acesso** painel.

![CreateKeyVault3]

Introduza o nome do certificado e clique em **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Opção personalizada
Ignore esta secção, se já tiver executado os passos a **básica** opção.

![SecurityCustomOption]

Terá do Cofre de chaves de origem, o URL de certificado e informações de thumbprint do certificado para concluir a página de segurança. Se não tiver em mãos, abrir outra janela do browser e no portal do Azure, faça o seguinte

1. Navegue para o serviço do Cofre de chaves.
2. Selecione o separador "Propriedades" e copie o "ID de recurso" a "Origem Cofre de chaves" na outra janela do browser 

    ![CertInfo0]

3. Agora, selecione o separador de "Certificados".
4. Clique na thumbprint do certificado, o que leva-o para a página de versões.
5. Clique em GUIDs abaixo a versão atual.

    ![CertInfo1]

6. Agora deve estar no ecrã, como abaixo. Copie o Thumbprint de SHA-1 hexadecimal para "Thumbprint do certificado" na outra janela do browser
7. Copie o "segredo"identificador para o "URL do certificado" na outra janela do browser.

    ![CertInfo2]

Verifique os **configurar definições avançadas** caixa introduzir certificados de cliente para **cliente administrativo** e **cliente só de leitura**. Nestes campos, introduza o thumbprint do seu certificado de cliente de administrador e o thumbprint do seu certificado de cliente de utilizador só de leitura, se aplicável. Quando os administradores tentam ligar ao cluster, é-lhes concedida acesso apenas se tiverem um certificado com um thumbprint que corresponda os valores de thumbprint introduzido aqui.  

### <a name="4-summary"></a>4. Resumo

Agora, está pronto para implementar o cluster. Antes de o fazer, transfira o certificado, procure dentro da caixa de informativa azul grande para a ligação. Certifique-se manter o certificado num local seguro. terá de se ligar ao seu cluster. Uma vez que o certificado que transferiu não tiver uma palavra-passe, recomenda-se adicionar um.

Para concluir a criação de cluster, clique em **criar**. Opcionalmente, pode transferir o modelo.

![Resumo]

Pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino", junto a barra de estado no canto superior direito do ecrã.) Se clicou em **Afixar ao Startboard** ao criar o cluster, verá **Implementar o Cluster do Service Fabric** afixado ao painel de **Início**. Este processo irá demorar algum tempo. 

Para efetuar operações de gestão no seu cluster com o Powershell ou CLI, terá de ligar ao cluster, leia mais sobre como na [ligar ao seu cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Ver o seu estado de cluster
![Captura de ecrã de detalhes do cluster no dashboard.][ClusterDashboard]

Depois do cluster é criado, pode inspecionar o seu cluster no portal do:

1. Aceda a **navegue** e clique em **Clusters do Service Fabric**.
2. Localize o cluster e clique no mesmo.
3. Agora pode ver os detalhes do seu cluster no dashboard, incluindo o ponto final público do cluster e uma ligação para o Service Fabric Explorer.

O **Monitor de nó** secção no painel de dashboard do cluster indica o número de VMs que estão em bom estado e estado de funcionamento incorreto. Pode encontrar mais detalhes sobre a integridade do cluster [introdução de modelo de estado de funcionamento do Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Clusters do Service Fabric necessitam de um determinado número de nós para ficar operacional sempre manter a disponibilidade e preservar o estado - referido como "manter o quórum". Portanto, normalmente, não é seguro encerrar todas as máquinas no cluster, a menos que o utilizador tiver sido executado pela primeira vez um [cópia de segurança completa do seu estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Ligar remotamente a uma instância de conjunto de dimensionamento de Máquina Virtual ou um nó de cluster
Cada um o NodeTypes especifique os resultados num conjunto de dimensionamento de Máquina Virtual ao obter a configuração do cluster. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Passos Seguintes
Neste ponto, tem um cluster seguro utilizar certificados para autenticação de gestão. Em seguida, [ligar ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerir segredos da aplicação](service-fabric-application-secret-management.md).  Além disso, saiba mais sobre [opções de suporte do Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
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
