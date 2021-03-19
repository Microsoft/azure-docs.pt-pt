---
title: Create a Service Fabric cluster in the Azure portal (Criar um cluster do Service Fabric no portal do Azure)
description: Aprenda a configurar um cluster de tecido de serviço seguro em Azure usando o portal Azure e o Cofre de Chaves Azure.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563731"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um cluster de tecido de serviço em Azure usando o portal Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que o acompanha através dos passos da criação de um cluster de Tecido de Serviço (Linux ou Windows) em Azure utilizando o portal Azure. Este guia acompanha-o através dos seguintes passos:

* Crie um aglomerado em Azure através do portal Azure.
* Autenticar administradores utilizando certificados.

> [!NOTE]
> Para opções de segurança mais avançadas, como a autenticação do utilizador com o Azure Ative Directory e a criação de certificados para segurança de aplicações, [crie o seu cluster utilizando o Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Segurança do cluster 
Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [Service Fabric cluster security scenarios (Cenários de segurança do cluster do Service Fabric)][service-fabric-cluster-security].

Se esta for a primeira vez que está a criar um cluster de tecidos de serviço ou estiver a implementar um cluster para cargas de trabalho de teste, pode saltar para a secção seguinte (**Criar cluster no portal Azure**) e ter o sistema a gerar certificados necessários para os seus clusters que executam cargas de trabalho de teste. Se está a criar um cluster para cargas de trabalho de produção, então continue a ler.

#### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e servidor (obrigatório)
Este certificado é necessário para proteger um cluster e impedir o acesso não autorizado ao mesmo. Proporciona segurança de cluster de algumas maneiras:

* **Autenticação do cluster:** Autentica comunicação nó-a-nó para a federação de clusters. Só os nós que possam provar a sua identidade com este certificado podem juntar-se ao cluster.
* **Autenticação do servidor:** Autentica os pontos finais de gestão de clusters a um cliente de gestão, para que o cliente de gestão saiba que está a falar com o cluster real. Este certificado também fornece TLS para a API de gestão HTTPS e para o Service Fabric Explorer em HTTPS.

Para servir estas finalidades, o certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um ficheiro De troca de informações pessoais (.pfx).
* O nome do **certificado deve coincidir com o domínio** utilizado para aceder ao cluster de Tecido de Serviço. Isto é necessário para fornecer TLS para os pontos finais de gestão HTTPS do cluster e Explorador de Tecido de Serviço. Não é possível obter um certificado TLS/SSL de uma autoridade de certificados (CA) para o `.cloudapp.azure.com` domínio. Adquira um nome de domínio personalizado para o seu cluster. Quando solicitar um certificado a um CA, o nome do certificado deve corresponder ao nome de domínio personalizado utilizado para o seu cluster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticação do cliente
Certificados de cliente adicionais autenticam administradores para tarefas de gestão de clusters. O Tecido de Serviço tem dois níveis de acesso: **administrador** e **utilizador apenas de leitura.** No mínimo, deve ser utilizado um único certificado de acesso administrativo. Para um acesso adicional ao nível do utilizador, deve ser fornecido um certificado separado. Para obter mais informações sobre funções de acesso, consulte [o controlo de acesso baseado em funções para clientes do Service Fabric][service-fabric-cluster-security-roles].

Não precisa de enviar certificados de autenticação do Cliente para o Key Vault para trabalhar com o Service Fabric. Estes certificados só devem ser fornecidos aos utilizadores autorizados para a gestão do cluster. 

> [!NOTE]
> O Azure Ative Directory é a forma recomendada de autenticar clientes para operações de gestão de clusters. Para utilizar o Azure Ative Directory, tem de [criar um cluster utilizando o Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de inscrição (opcional)
Qualquer número de certificados adicionais pode ser instalado num cluster para fins de segurança da aplicação. Antes de criar o seu cluster, considere os cenários de segurança da aplicação que requerem que um certificado seja instalado nos nós, tais como:

* Encriptação e desencriptação dos valores de configuração da aplicação
* Encriptação de dados através de nóles durante a replicação 

Os certificados de inscrição não podem ser configurados ao [criar um cluster através do portal Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Para configurar os certificados de aplicação na hora de configuração do cluster, tem de [criar um cluster utilizando o Azure Resource Manager][create-cluster-arm]. Também pode adicionar certificados de inscrição ao cluster depois de ter sido criado.

## <a name="create-cluster-in-the-azure-portal"></a>Criar cluster no portal Azure

Criar um cluster de produção para atender às suas necessidades de aplicação envolve algum planeamento, para o ajudar, é fortemente recomendado que leia e compreenda o documento de planeamento do [Cluster de Tecidos][service-fabric-cluster-capacity] de Serviço. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Pesquisa do recurso cluster de Tecido de Serviço

Inicie sessão no [portal do Azure][azure-portal].
Clique **em Criar um recurso** para adicionar um novo modelo de recursos. Procure o modelo de cluster de tecido de serviço no **Mercado** em **"Everything".**
Selecione **O Cluster de Tecido de Serviço** da lista.

![pesquisa por modelo de cluster de tecido de serviço no portal Azure.][SearchforServiceFabricClusterTemplate]

Navegue para a lâmina do **cluster de tecido** de serviço e clique em **Criar**.

A lâmina **de cluster De Tecido de Serviço Create** tem os seguintes quatro passos:

### <a name="1-basics"></a>1. Básicos
![Screenshot de criar um novo grupo de recursos.][CreateRG]

Na lâmina Basics, você precisa fornecer os detalhes básicos para o seu cluster.

1. Insira o nome do seu agrupamento.
2. Introduza um **nome de utilizador** e **palavra-passe** para desktop remoto para os VMs.
3. Certifique-se de selecionar a **Subscrição** para a qual deseja que o seu cluster seja implantado, especialmente se tiver várias subscrições.
4. Criar um novo **grupo de Recursos.** É melhor dar-lhe o mesmo nome que o cluster, uma vez que ajuda a encontrá-los mais tarde, especialmente quando está a tentar fazer alterações na sua implantação ou apagar o seu cluster.
   
   > [!NOTE]
   > Embora possa decidir usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isto facilita a eliminação de clusters e todos os recursos que utiliza.
   > 
   > 
5. Selecione a **Localização** em que pretende criar o cluster. Se está a planear usar um certificado existente que já carregou para um cofre chave, deve usar a mesma região em que o cofre key está. 

### <a name="2-cluster-configuration"></a>2. Configuração do cluster
![Criar um tipo de nó][CreateNodeType]

Configure os seus nós de cluster. Os tipos de nó definem os tamanhos de VM, o número de VMs e as suas propriedades. O seu cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário (o primeiro que define no portal) deve ter pelo menos cinco VMs, já que este é o tipo de nó onde os serviços de sistema de Sistema de Tecido de Serviço são colocados. Não configuure **propriedades de colocação** porque uma propriedade de colocação padrão de "NodeTypeName" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nós é uma aplicação que contém um serviço frontal e um serviço de back-end. Pretende colocar o serviço frontal em VMs mais pequenos (tamanhos VM como D2_V2) com portas abertas à Internet, e colocar o serviço de back-end em VMs maiores (com tamanhos VM como D3_V2, D6_V2, D15_V2, e assim por diante) sem portas viradas para a Internet abertas.
> 

1. Escolha um nome para o seu tipo de nó (1 a 12 caracteres contendo apenas letras e números).
2. O **tamanho** mínimo de VMs para o tipo de nó primário é impulsionado pelo **nível de durabilidade** que escolhe para o cluster. O padrão para o nível de durabilidade é o bronze. Para obter mais informações sobre a durabilidade, consulte [como escolher a durabilidade do cluster de tecido de serviço.][service-fabric-cluster-durability]
3. Selecione o **tamanho da máquina Virtual.** Os VMs da série D têm unidades SSD e são altamente recomendados para aplicações imponentes. Não utilize nenhum SKU VM que tenha núcleos parciais ou tenha menos de 10 GB de capacidade de disco disponível. Consulte o documento de [consideração do planeamento do cluster de tecido][service-fabric-cluster-capacity] de serviço para obter ajuda na seleção do tamanho de VM.
4.  **Um único aglomerado de nó e três aglomerados de nó são destinados apenas** para utilização de testes. Não são apoiados por quaisquer cargas de trabalho de produção em funcionamento.
5. Escolha a **capacidade inicial de conjunto de balança de máquina virtual** para o tipo de nó. Pode escalar o número de VMs num nó mais tarde, mas no tipo de nó primário, o mínimo é de cinco para cargas de trabalho de produção. Outros tipos de nó podem ter um mínimo de um VM. O **número** mínimo de VMs para o tipo de nó primário impulsiona a **fiabilidade** do seu cluster.  
6. Configurar **pontos finais personalizados**. Este campo permite-lhe introduzir uma lista de portas separadas por vírgula que pretende expor através do Azure Load Balancer para a Internet pública para as suas aplicações. Por exemplo, se planeia implementar uma aplicação web no seu cluster, insira aqui "80" para permitir o tráfego na porta 80 no seu cluster. Para obter mais informações sobre os pontos finais, consulte [a comunicação com as aplicações][service-fabric-connect-and-communicate-with-services]
7. **Ativar o proxy invertido**.  O [proxy reverso do Service Fabric](service-fabric-reverseproxy.md) ajuda os microserviços a funcionar num cluster de Tecidos de Serviço a descobrir e comunicar com outros serviços que tenham pontos finais http.
8. De volta à lâmina de **configuração cluster,** em **+Mostrar definições opcionais,** configurar **diagnósticos** de cluster . Por padrão, os diagnósticos são ativados no seu cluster para ajudar com problemas de resolução de problemas. Se pretender desativar os diagnósticos, altere o alterar o alterar o **'Status'** para **Off**. **Não** é aconselhável desligar os diagnósticos. Se já tem o projeto Application Insights criado, então dê a sua chave, para que os vestígios da aplicação sejam encaminhados para ele.
9. **Incluir o serviço DNS**.  O [serviço DNS é](service-fabric-dnsservice.md) um serviço opcional que lhe permite encontrar outros serviços utilizando o protocolo DNS.
10. Selecione o **modo de atualização do Tecido** para o quais pretende definir o seu cluster. Selecione **Automatic**, se quiser que o sistema recolha automaticamente a versão mais recente disponível e tente atualizar o seu cluster para o mesmo. Desa ajuste o modo **manual,** se pretender escolher uma versão suportada. Para obter mais detalhes sobre o modo de atualização do Tecido consulte o [documento de upgrade do Cluster de Tecido de Serviço.][service-fabric-cluster-upgrade]

> [!NOTE]
> Apoiamos apenas clusters que estão a executar versões suportadas do Service Fabric. Ao selecionar o modo **Manual,** está a assumir a responsabilidade de atualizar o seu cluster para uma versão suportada.
> 

### <a name="3-security"></a>3. Segurança
![Screenshot das configurações de segurança no portal Azure.][BasicSecurityConfigs]

Para facilitar a criação de um cluster de teste seguro, fornecemos a opção **Básica.** Se já tem um certificado e o carregou para o cofre da [chave](../key-vault/index.yml) (e ativou o cofre de chaves para implantação), use a opção **Custom**

#### <a name="basic-option"></a>Opção Básica
Siga os ecrãs para adicionar ou reutilizar um cofre de chaves existente e adicione um certificado. A adição do certificado é um processo sincronizado e por isso terá de esperar pela criação do certificado.

Resista à tentação de navegar para longe do ecrã até que o processo anterior esteja concluído.

![A screenshot mostra a página de Segurança com Basic selecionada com o painel de abóbada chave e criar o painel do cofre da chave.][CreateKeyVault]

Agora que o cofre chave foi criado, edite as políticas de acesso para o seu cofre chave. 

![O screenshot mostra o painel de cluster do Tecido de Serviço Create com a opção 3 Segurança selecionada e uma explicação de que o cofre da chave não está ativado.][CreateKeyVault2]

Clique nas **políticas de acesso editar,** em seguida, mostre políticas de acesso **avançadas** e permita o acesso às Máquinas Virtuais Azure para implementação. Recomenda-se que também ative a implementação do modelo. Depois de ter feito as suas seleções, não se esqueça de clicar no botão **Guardar** e fechar o painel de **políticas de Acesso.**

![O screenshot mostra o painel de cluster do Tecido de Serviço Create com o painel de segurança aberto e o painel de políticas de acesso aberto.][CreateKeyVault3]

Insira o nome do certificado e clique **em OK**.

![O screenshot mostra o painel de cluster Do Tecido de Serviço Create com segurança selecionado como antes, mas sem a explicação de que o cofre da chave não está ativado.][CreateKeyVault4]

#### <a name="custom-option"></a>Opção personalizada
Ignore esta secção, se já tiver realizado os passos na Opção **Básica.**

![A screenshot mostra a caixa de diálogo de configuração de configuração do cluster de segurança de configuração de segurança.][SecurityCustomOption]

Precisa do cofre de chave Fonte, URL de certificado e informação de impressão digital do certificado para completar a página de segurança. Se não o tiver à mão, abra outra janela do navegador e no portal Azure faça o seguinte

1. Navegue para o seu serviço de cofre chave.
2. Selecione o separador "Propriedades" e copie o 'RESOURCE ID' para "Cofre de chave de origem" na outra janela do navegador 

    ![A imagem mostra a janela propriedades para o cofre da chave.][CertInfo0]

3. Agora, selecione o separador "Certificados".
4. Clique na impressão digital do certificado, que o leva à página Versões.
5. Clique nos GUIDs que vê na versão atual.

    ![Screenshot mostra a janela do certificado para o cofre da chave][CertInfo1]

6. Deve estar agora no ecrã como abaixo. Copie a impressão digital hexadémica SHA-1 para "Impressão digital do certificado" na outra janela do navegador
7. Copie o 'Identificador Secreto' para o "URL de certificado" noutra janela do navegador.

    ![A screenshot mostra a caixa de diálogo da versão do certificado com a opção de copiar o identificador de certificado.][CertInfo2]

Consulte a caixa **de configurações avançadas Configure** para introduzir certificados de cliente para **cliente administrativo** e **cliente apenas de leitura**. Nestes campos, introduza a impressão digital do seu certificado de cliente administrativo e a impressão digital do seu certificado de cliente de leitura, se aplicável. Quando os administradores tentam ligar-se ao cluster, só lhes é concedido acesso se tiverem um certificado com uma impressão digital que corresponda aos valores de impressão digital introduzidos aqui.  

### <a name="4-summary"></a>4. Resumo

Agora estás pronto para implantar o aglomerado. Antes de fazer isso, faça o download do certificado, procure dentro da caixa informativa azul grande para o link. Certifique-se de manter o certificado em um lugar seguro. precisa dele para se ligar ao seu cluster. Uma vez que o certificado que descarregou não tem uma senha, é aconselhável adicionar uma.

Para completar a criação do cluster, clique em **Criar**. Você pode opcionalmente baixar o modelo.

![O Screenshot mostra a página de resumo do cluster Do Tecido de Serviço create com um link para visualizar e descarregar um certificado.][Summary]

Pode ver o progresso da criação nas notificações. (Clique no ícone "Bell" perto da barra de estado no canto superior direito do seu ecrã.) Se clicar em **Pin to Startboard** enquanto cria o cluster, vê **o Cluster de Tecido de Serviço de Implantação** fixado na placa **Iniciar.** Este processo vai levar algum tempo. 

Para realizar operações de gestão no seu cluster utilizando PowerShell ou CLI, precisa de se ligar ao seu cluster, ler mais sobre como [ligar-se ao seu cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Veja o seu estado de cluster
![Screenshot dos detalhes do cluster no painel de instrumentos.][ClusterDashboard]

Uma vez criado o seu cluster, pode inspecionar o seu cluster no portal:

1. Vá a **Procurar** e clique em **Service Fabric Clusters**.
2. Localize o seu cluster e clique nele.
3. Agora pode ver os detalhes do seu cluster no dashboard, incluindo o ponto final público do cluster e uma ligação para o Service Fabric Explorer.

A secção **de monitor de nó** na lâmina do painel do cluster indica o número de VMs que são saudáveis e não saudáveis. Pode encontrar mais detalhes sobre a saúde do cluster na introdução do modelo de [saúde Service Fabric.][service-fabric-health-introduction]

> [!NOTE]
> Os clusters de tecidos de serviço requerem que um certo número de nós esteja sempre de pé para manter a disponibilidade e preservar o estado - referido como "manutenção do quórum". Por isso, normalmente não é seguro desligar todas as máquinas do cluster, a menos que tenha realizado pela primeira vez uma [cópia de segurança completa do seu estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Ligação remota a uma placa de conjunto de escala de máquina virtual ou um nó de cluster
Cada um dos NodeTypes que especifica no seu cluster resulta numa configuração de escala de máquina virtual. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Passos seguintes
Neste momento, tem um cluster seguro usando certificados para autenticação de gestão. Em seguida, [ligue-se ao seu cluster](service-fabric-connect-to-secure-cluster.md) e aprenda a gerir segredos de [aplicação.](service-fabric-application-secret-management.md)  Além disso, saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
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
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
