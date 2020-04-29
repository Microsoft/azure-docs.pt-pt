---
title: Create a Service Fabric cluster in the Azure portal (Criar um cluster do Service Fabric no portal do Azure)
description: Aprenda a configurar um cluster de tecido de serviço seguro em Azure utilizando o portal Azure e o Cofre chave Azure.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: e0cd3d5e5a37720134a5bce596bba211b375f19d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458322"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um cluster de tecido de serviço em Azure utilizando o portal Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que o percorre através dos degraus da criação de um cluster de tecido de serviço (Linux ou Windows) em Azure usando o portal Azure. Este guia acompanha-o pelos seguintes passos:

* Crie um cluster em Azure através do portal Azure.
* Autenticar administradores usando certificados.

> [!NOTE]
> Para opções de segurança mais avançadas, como a autenticação do utilizador com o Diretório Ativo Azure e a criação de certificados para a segurança da aplicação, [crie o seu cluster utilizando][create-cluster-arm]o Gestor de Recursos Azure .
> 
> 

## <a name="cluster-security"></a>Segurança do cluster 
Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [Service Fabric cluster security scenarios (Cenários de segurança do cluster do Service Fabric)][service-fabric-cluster-security].

Se esta for a primeira vez que está a criar um cluster de tecido de serviço ou está a implantar um cluster para trabalhos de trabalho de teste, pode saltar para a secção seguinte (**Criar cluster no Portal Azure)** e fazer com que o sistema gere certificados necessários para os seus clusters que executam cargas de trabalho de teste. Se estiver a criar um cluster para cargas de trabalho de produção, continue a ler.

#### <a name="cluster-and-server-certificate-required"></a>Cluster e certificado de servidor (obrigatório)
Este certificado é necessário para proteger um cluster e impedir o acesso não autorizado ao mesmo. Fornece segurança de cluster de várias maneiras:

* **Autenticação do cluster:** Autentica comunicação nó-a-nó para a federação de agrupamentos. Só nós que possam provar a sua identidade com este certificado podem aderir ao cluster.
* **Autenticação do servidor:** Autentica os pontos finais da gestão do cluster para um cliente de gestão, para que o cliente de gestão saiba que está a falar com o cluster real. Este certificado também fornece TLS para a API de gestão HTTPS e para Service Fabric Explorer em HTTPS.

Para cumprir estes fins, o certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um ficheiro de Intercâmbio de Informações Pessoais (.pfx).
* O nome do **certificado deve coincidir com o domínio** utilizado para aceder ao cluster Service Fabric. Isto é necessário para fornecer TLS para os pontos finais de gestão HTTPS do cluster e Service Fabric Explorer. Não é possível obter um certificado TLS/SSL de `.cloudapp.azure.com` uma autoridade de certificados (CA) para o domínio. Adquira um nome de domínio personalizado para o seu cluster. Quando solicitar um certificado a partir de um CA, o nome do certificado deve corresponder ao nome de domínio personalizado utilizado para o seu cluster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticação do cliente
Certificados adicionais de cliente autenticam administradores para tarefas de gestão de clusters. O Serviço Fabric tem dois níveis de acesso: **administração** e **utilizador apenas de leitura.** No mínimo, deve ser utilizado um único certificado de acesso administrativo. Para um acesso adicional ao nível do utilizador, deve ser fornecido um certificado separado. Para obter mais informações sobre as funções de acesso, consulte [o controlo de acesso baseado em papéis para os clientes do Service Fabric][service-fabric-cluster-security-roles].

Não precisa de enviar certificados de autenticação do Cliente para o Key Vault para trabalhar com o Service Fabric. Estes certificados só precisam de ser fornecidos aos utilizadores autorizados para a gestão do cluster. 

> [!NOTE]
> O Azure Ative Directory é a forma recomendada de autenticar clientes para operações de gestão de clusters. Para utilizar o Diretório Ativo Azure, deve criar um cluster utilizando o Gestor de [Recursos Azure][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de candidatura (opcional)
Qualquer número de certificados adicionais pode ser instalado num cluster para efeitos de segurança da aplicação. Antes de criar o seu cluster, considere os cenários de segurança da aplicação que exigem a instalação de um certificado nos nós, tais como:

* Encriptação e desencriptação dos valores de configuração da aplicação
* Encriptação de dados em nódosos durante a replicação 

Os certificados de candidatura não podem ser configurados na [criação de um cluster através do portal Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Para configurar os certificados de aplicação no tempo de configuração do cluster, deve criar um cluster utilizando o Gestor de [Recursos Azure][create-cluster-arm]. Também pode adicionar certificados de candidatura ao cluster depois de criado.

## <a name="create-cluster-in-the-azure-portal"></a>Criar cluster no portal Azure

Criar um cluster de produção para atender às necessidades da sua aplicação envolve algum planeamento, para o ajudar com isso, recomenda-se vivamente que leia e compreenda o documento de considerações de planeamento do Cluster de [Tecidos de Serviço.][service-fabric-cluster-capacity] 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Pesquisa do recurso cluster Service Fabric

Inicie sessão no [portal do Azure][azure-portal].
Clique **em Criar um recurso** para adicionar um novo modelo de recursos. Procure o modelo de Cluster de Tecido de Serviço no **Mercado** em **Tudo**.
Selecione **Cluster de Tecido de Serviço** da lista.

![pesquisar por modelo de cluster de tecido de serviço no portal Azure.][SearchforServiceFabricClusterTemplate]

Navegue para a lâmina **de cluster** de tecido de serviço e clique em **Criar**.

A lâmina de **cluster Create Service Fabric** tem os seguintes quatro passos:

### <a name="1-basics"></a>1. Bases
![Screenshot de criar um novo grupo de recursos.][CreateRG]

Na lâmina Basics, você precisa fornecer os detalhes básicos para o seu cluster.

1. Insira o nome do seu aglomerado.
2. Introduza um nome de **utilizador** e **palavra-passe** para desktop remoto para os VMs.
3. Certifique-se de selecionar a **Subscrição** para a qual pretende que o seu cluster seja implantado, especialmente se tiver várias subscrições.
4. Criar um novo **grupo de Recursos.** É melhor dar-lhe o mesmo nome que o cluster, uma vez que ajuda a encontrá-los mais tarde, especialmente quando está a tentar fazer alterações na sua implementação ou apagar o seu cluster.
   
   > [!NOTE]
   > Embora possa decidir usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isto facilita a eliminação de clusters e todos os recursos que utiliza.
   > 
   > 
5. Selecione o **Local** em que pretende criar o cluster. Se está a planear usar um certificado existente que já carregou para um cofre chave, tem de usar a mesma região em que o seu cofre Key está. 

### <a name="2-cluster-configuration"></a>2. Configuração do cluster
![Criar um tipo de nó][CreateNodeType]

Configure os seus nós de cluster. Os tipos de nó definem os tamanhos vm, o número de VMs e as suas propriedades. O seu cluster pode ter mais do que um nó, mas o tipo de nó primário (o primeiro que define no portal) deve ter pelo menos cinco VMs, já que este é o tipo de nó onde os serviços de sistema service Fabric são colocados. Não configure propriedades de **colocação** porque uma propriedade de colocação padrão de "NodeTypeName" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nó é uma aplicação que contém um serviço frontal e um serviço de back-end. Você quer colocar o serviço frontal em VMs menores (tamanhos VM como D2_V2) com portas abertas à Internet, e colocar o serviço de back-end em VMs maiores (com tamanhos VM como D3_V2, D6_V2, D15_V2, e assim por diante) sem portas viradas para a Internet abertas.
> 

1. Escolha um nome para o seu tipo de nó (1 a 12 caracteres que contenham apenas letras e números).
2. O **tamanho** mínimo de VMs para o tipo de nó primário é conduzido pelo **nível de Durabilidade** que escolher para o cluster. O padrão para o nível de durabilidade é o bronze. Para obter mais informações sobre durabilidade, consulte [como escolher a durabilidade do cluster do Tecido de Serviço][service-fabric-cluster-durability].
3. Selecione o tamanho da **máquina Virtual**. Os VMs da série D têm unidades SSD e são altamente recomendados para aplicações imponentes. Não utilize nenhum VM SKU que tenha núcleos parciais ou tenha menos de 10 GB de capacidade de disco disponível. Consulte o documento de [consideração][service-fabric-cluster-capacity] de planeamento de cluster de tecido de serviço para obter ajuda na seleção do tamanho vm.
4.  **O aglomerado de nó único e três aglomerados** de nós destinam-se apenas ao ensaio. Não são apoiados para qualquer carga de trabalho de produção em execução.
5. Escolha a capacidade definida de **escala VM inicial** para o tipo de nó. Pode aumentar ou descer o número de VMs num nó mais tarde, mas no tipo de nó primário, o mínimo é de cinco para cargas de trabalho de produção. Outros tipos de nó podem ter um mínimo de um VM. O **número** mínimo de VMs para o tipo de nó primário impulsiona a **fiabilidade** do seu cluster.  
6. Configurar **pontos finais personalizados**. Este campo permite-lhe introduzir uma lista separada de portas que pretende expor através do Azure Load Balancer à Internet pública para as suas aplicações. Por exemplo, se planeia implementar uma aplicação web para o seu cluster, introduza "80" aqui para permitir o tráfego na porta 80 no seu cluster. Para obter mais informações sobre pontos finais, consulte [a comunicação com aplicações][service-fabric-connect-and-communicate-with-services]
7. **Ativar o proxy invertido**.  O [proxy inverso do Tecido de Serviço](service-fabric-reverseproxy.md) ajuda os microserviços a funcionar num cluster de Tecido de Serviço a descobrir e a comunicar com outros serviços que têm pontos finais.
8. De volta à lâmina de configuração do **Cluster,** em **+Mostrar configurações opcionais,** configurar **diagnósticos**de cluster . Por padrão, os diagnósticos estão ativados no seu cluster para ajudar com problemas de resolução de problemas. Se pretender desativar os diagnósticos, altere o **estado** para **desligar**. **Não** é aconselhável desligar os diagnósticos. Se já tem o projeto Application Insights criado, então dê a sua chave, para que os vestígios de aplicação sejam encaminhados para ele.
9. **Incluir o serviço DNS**.  O [serviço DNS é](service-fabric-dnsservice.md) um serviço opcional que lhe permite encontrar outros serviços utilizando o protocolo DNS.
10. Selecione o modo de **atualização do Tecido** para o segundo em que pretende definir o seu cluster. Selecione **Automatic**, se quiser que o sistema pegue automaticamente a versão mais recente disponível e tente atualizar o seu cluster para a sua versão. Desajuste o modo para **Manual,** se quiser escolher uma versão suportada. Para mais detalhes sobre o modo de atualização do Tecido, consulte o documento de upgrade do cluster de [tecido de serviço.][service-fabric-cluster-upgrade]

> [!NOTE]
> Apoiamos apenas clusters que estão a executar versões suportadas do Service Fabric. Ao selecionar o modo **Manual,** está a assumir a responsabilidade de atualizar o seu cluster para uma versão suportada.
> 

### <a name="3-security"></a>3. Segurança
![Screenshot das configurações de segurança no portal Azure.][BasicSecurityConfigs]

Para facilitar a criação de um cluster de teste seguro para si, fornecemos a opção **Basic.** Se já tem um certificado e o carregou para o seu [cofre de chaves](/azure/key-vault/) (e ativou o cofre chave para a implantação), use a opção **Custom**

#### <a name="basic-option"></a>Opção Básica
Siga os ecrãs para adicionar ou reutilizar um cofre chave existente e adicionar um certificado. A adição do certificado é um processo sincronizado e por isso terá de esperar pela criação do certificado.

Resista à tentação de navegar para longe do ecrã até que o processo anterior esteja concluído.

![CreateKeyVault]

Agora que o cofre chave foi criado, edite as políticas de acesso para o seu cofre chave. 

![CreateKeyVault2]

Clique nas políticas de **acesso editar,** em **seguida, mostrar políticas avançadas** de acesso e permitir o acesso às Máquinas Virtuais Azure para implementação. Recomenda-se que também ative a implementação do modelo. Depois de ter feito as suas seleções, não se esqueça de clicar no botão **Guardar** e fechar fora do painel de políticas de **acesso.**

![CreateKeyVault3]

Introduza o nome do certificado e clique **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Opção Personalizada
Ignore esta secção, se já realizou os passos na Opção **Básica.**

![Opção SecurityCustomOption]

Precisa do cofre da chave Source, URL de certificado e informação de impressão digital do Certificado para completar a página de segurança. Se não tiver à mão, abra outra janela do navegador e no portal Azure faça o seguinte

1. Navegue para o seu serviço de cofre chave.
2. Selecione o separador "Propriedades" e copie o 'ID DE RECURSOS' para "Source key vault" na outra janela do navegador 

    ![CertInfo0]

3. Agora, selecione o separador "Certificados".
4. Clique na impressão digital do certificado, que o leva à página Versões.
5. Clique nos GUIDs que vê na versão atual.

    ![CertInfo1]

6. Agora deve estar no ecrã como abaixo. Copie a impressão digital hexadecimal SHA-1 para "Impressão digital do certificado" na outra janela do navegador
7. Copie o 'Identificador Secreto' para o "URL de certificado" noutra janela do navegador.

    ![CertInfo2]

Verifique a caixa de **configurações avançadas configure** para introduzir certificados de cliente para **cliente administrativo** e **cliente apenas de leitura**. Nestes campos, introduza a impressão digital do seu certificado de cliente administrativo e a impressão digital do seu certificado de cliente de utilizador apenas de leitura, se aplicável. Quando os administradores tentam ligar-se ao cluster, só lhes é concedido acesso se tiverem um certificado com uma impressão digital que corresponda aos valores de impressão digital introduzidos aqui.  

### <a name="4-summary"></a>4. Resumo

Agora estápronto para implantar o aglomerado. Antes de isso, faça o download do certificado, procure dentro da grande caixa informativa azul para o link. Certifique-se de manter o certificado num lugar seguro. precisa que se ligue ao seu aglomerado. Uma vez que o certificado que descarregou não tem senha, é aconselhável adicionar uma.

Para completar a criação do cluster, clique em **Criar**. Você pode baixar opcionalmente o modelo.

![Resumo]

Pode ver o progresso da criação nas notificações. (Clique no ícone "Bell" perto da barra de estado na parte superior direita do seu ecrã.) Se clicasse em **Pin to Startboard** enquanto criava o cluster, veria o Cluster de Tecido de Serviço de **Implantação** fixado na placa **Start.** Este processo levará algum tempo. 

Para efetuar operações de gestão no seu cluster utilizando powershell ou CLI, precisa de se ligar ao seu cluster, ler mais sobre como [ligar-se ao seu cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Veja o seu estado de cluster
![Screenshot dos detalhes do cluster no painel de instrumentos.][ClusterDashboard]

Assim que o seu cluster for criado, pode inspecionar o seu cluster no portal:

1. Vá **procurar** e clique em Clusters de **Tecidode Serviço**.
2. Localize o seu cluster e clique nele.
3. Agora pode ver os detalhes do seu cluster no dashboard, incluindo o ponto final público do cluster e uma ligação para o Service Fabric Explorer.

A secção **de Monitor** de Nó na lâmina do painel de instrumentos do cluster indica o número de VMs que são saudáveis e não são saudáveis. Pode encontrar mais detalhes sobre a saúde do cluster na introdução do modelo de [saúde Service Fabric.][service-fabric-health-introduction]

> [!NOTE]
> Serviço Os clusters de tecidos exigem que um certo número de nós esteja sempre para manter a disponibilidade e preservar o estado - referido como "manutenção do quórum". Portanto, normalmente não é seguro desligar todas as máquinas do cluster a menos que tenha realizado pela primeira vez uma [cópia de segurança completa do seu estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Ligação remota a uma instância de conjunto de escala de máquina virtual ou um nó de cluster
Cada um dos Nós que especifica no seu cluster resulta num conjunto de escala de máquina virtual. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Passos seguintes
Neste ponto, tem um cluster seguro usando certificados para autenticação de gestão. Em seguida, [ligue-se ao seu cluster](service-fabric-connect-to-secure-cluster.md) e aprenda a gerir os segredos da [aplicação.](service-fabric-application-secret-management.md)  Além disso, conheça as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
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
[Opção SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Resumo]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
