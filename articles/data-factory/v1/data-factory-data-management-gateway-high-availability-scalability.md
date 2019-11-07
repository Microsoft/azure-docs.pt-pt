---
title: Alta disponibilidade com o gateway de gerenciamento de dados no Azure Data Factory
description: Este artigo explica como você pode escalar horizontalmente um gateway de gerenciamento de dados adicionando mais nós e escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: c3428019fe23e3f206e763249a18e7774bab149b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682700"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Gateway de Gerenciamento de Dados-alta disponibilidade e escalabilidade (visualização)
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [tempo de execução de integração auto-hospedado no](../create-self-hosted-integration-runtime.md). 


Este artigo ajuda você a configurar a solução de alta disponibilidade e escalabilidade com Gerenciamento de Dados gateway/integração.    

> [!NOTE]
> Este artigo pressupõe que você já esteja familiarizado com noções básicas de Integration Runtime (anteriormente Gerenciamento de Dados gateway). Se você não estiver, consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md).
> 
> **Esse recurso de visualização tem suporte oficialmente no gerenciamento de dados gateway versão 2.12. xxxx. x e superior**. Verifique se você está usando a versão 2.12. xxxx. x ou superior. Baixe a versão mais recente do gateway de Gerenciamento de Dados [aqui](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Descrição geral
Você pode associar gateways de gerenciamento de dados que estão instalados em vários computadores locais com um único gateway lógico do Portal. Esses computadores são chamados de **nós**. Você pode ter até **quatro nós** associados a um gateway lógico. Os benefícios de ter vários nós (computadores locais com o gateway instalado) para um gateway lógico são:  

- Melhore o desempenho da movimentação de dados entre armazenamentos de dados locais e na nuvem.  
- Se um dos nós ficar inativo por algum motivo, outros nós ainda estarão disponíveis para mover os dados. 
- Se um dos nós precisar ser colocado offline para manutenção, outros nós ainda estarão disponíveis para mover os dados.

Você também pode configurar o número de **trabalhos de movimentação de dados simultâneos** que podem ser executados em um nó para escalar verticalmente a capacidade de mover dados entre armazenamentos de dados locais e na nuvem. 

Usando o portal do Azure, você pode monitorar o status desses nós, o que ajuda a decidir se deseja adicionar ou remover um nó do gateway lógico. 

## <a name="architecture"></a>Arquitetura 
O diagrama a seguir fornece a visão geral da arquitetura do recurso de escalabilidade e disponibilidade do gateway de Gerenciamento de Dados: 

![Gateway de Gerenciamento de Dados-alta disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Um **Gateway lógico** é o gateway que você adiciona a um data factory no portal do Azure. Anteriormente, você poderia associar apenas um computador local do Windows com Gerenciamento de Dados gateway instalado com um gateway lógico. Esse computador de gateway local é chamado de nó. Agora, você pode associar até **quatro nós físicos** a um gateway lógico. Um gateway lógico com vários nós é chamado de **Gateway de vários nós**.  

Todos esses nós estão **ativos**. Todos eles podem processar trabalhos de movimentação de dados para mover dados entre armazenamentos de dados locais e na nuvem. Um dos nós atua como Dispatcher e Worker. Outros nós nos grupos são nós de trabalho. Um nó de **Dispatcher** efetua pull de tarefas/trabalhos de movimentação de dados do serviço de nuvem e os distribui para nós de trabalho (incluindo ele mesmo). Um nó de **trabalho** executa trabalhos de movimentação de dados para mover dados entre armazenamentos de dados locais e na nuvem. Todos os nós são trabalhadores. Somente um nó pode ser expedição e trabalho.    

Normalmente, você pode começar com um nó e **escalar horizontalmente** para adicionar mais nós, já que os nós existentes estão sobrecarregados com a carga de movimentação de dados. Você também pode **escalar verticalmente** o recurso de movimentação de dados de um nó de gateway aumentando o número de trabalhos simultâneos que têm permissão para serem executados no nó. Esse recurso também está disponível com um gateway de nó único (mesmo quando o recurso de escalabilidade e disponibilidade não está habilitado). 

Um gateway com vários nós mantém as credenciais do armazenamento de dados sincronizadas em todos os nós. Se houver um problema de conectividade de nó para nó, as credenciais poderão estar fora de sincronia. Quando você define as credenciais para um armazenamento de dados local que usa um gateway, ele salva as credenciais no nó Dispatcher/Worker. O nó Dispatcher sincroniza com outros nós de trabalho. Esse processo é conhecido como **sincronização de credenciais**. O canal de comunicação entre os nós pode ser **criptografado** por um certificado SSL/TLS público. 

## <a name="set-up-a-multi-node-gateway"></a>Configurar um gateway de vários nós
Esta seção pressupõe que você tenha passado pelos dois artigos a seguir ou esteja familiarizado com os conceitos nestes artigos: 

- [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) – fornece uma visão geral detalhada do gateway.
- [Mover dados entre armazenamentos de dados locais e em nuvem](data-factory-move-data-between-onprem-and-cloud.md) -contém um passo a passos com instruções passo a passo para usar um gateway com um único nó.  

> [!NOTE]
> Antes de instalar um gateway de gerenciamento de dados em um computador Windows local, consulte pré-requisitos listados no [artigo principal](data-factory-data-management-gateway.md#prerequisites).

1. No [passo a passo](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), ao criar um gateway lógico, habilite o recurso de **escalabilidade de alta disponibilidade &** . 

    ![Gerenciamento de Dados gateway – habilitar alta disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na página **Configurar** , use o link **instalação expressa** ou **configuração manual** para instalar um gateway no primeiro nó (um computador Windows local).

    ![Gerenciamento de Dados gateway – Express ou configuração manual](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se você usar a opção de instalação expressa, a comunicação de nó para nó será feita sem criptografia. O nome do nó é o mesmo que o nome do computador. Use a configuração manual se a comunicação de nó nó precisar ser criptografada ou se você quiser especificar um nome de nó de sua escolha. Os nomes de nó não podem ser editados posteriormente.
3. Se você escolher **instalação expressa**
    1. Você verá a seguinte mensagem depois que o gateway for instalado com êxito:

        ![Gerenciamento de Dados gateway-êxito na instalação do Express](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Inicie o Configuration Manager de Gerenciamento de Dados para o gateway seguindo [estas instruções](data-factory-data-management-gateway.md#configuration-manager). Você vê o nome do gateway, o nome do nó, o status, etc.

        ![Gateway de Gerenciamento de Dados-instalação bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se você escolher **configuração manual**:
    1. Baixe o pacote de instalação do centro de download da Microsoft, execute-o para instalar o gateway em seu computador.
    2. Use a **chave de autenticação** da página **Configurar** para registrar o gateway.
    
        ![Gateway de Gerenciamento de Dados-instalação bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na página **novo nó do gateway** , você pode fornecer um **nome** personalizado para o nó do gateway. Por padrão, o nome do nó é o mesmo que o nome do computador.    

        ![Gateway de Gerenciamento de Dados-especifique o nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na próxima página, você pode escolher se deseja **habilitar a criptografia para comunicação de nó para nó**. Clique em **ignorar** para desabilitar a criptografia (padrão).

        ![Gerenciamento de Dados gateway – habilitar a criptografia](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Só há suporte para a alteração do modo de criptografia quando você tem um único nó de gateway no gateway lógico. Para alterar o modo de criptografia quando um gateway tiver vários nós, execute as seguintes etapas: excluir todos os nós, exceto um nó, alterar o modo de criptografia e, em seguida, adicionar os nós novamente.
        > 
        > Consulte a seção [requisitos de certificado TLS/SSL](#tlsssl-certificate-requirements) para obter uma lista de requisitos para usar um certificado TLS/SSL. 
    5. Depois que o gateway for instalado com êxito, clique em Iniciar Configuration Manager:
    
        ![Instalação manual-iniciar o Configuration Manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Você vê Gerenciamento de Dados Configuration Manager de gateway no nó (computador Windows local), que mostra o status de conectividade, o **nome do gateway**e o nome do **nó**.  

        ![Gateway de Gerenciamento de Dados-instalação bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se você estiver Provisionando o gateway em uma VM do Azure, poderá usar [esse Azure Resource Manager modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Esse script cria um gateway lógico, configura as VMs com o software Gerenciamento de Dados gateway instalado e registra-as com o gateway lógico. 
6. Em portal do Azure, inicie a página **Gateway** : 
    1. Na data factory home page no portal, clique em **Serviços vinculados**.
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Selecione o **Gateway** para ver a página do **Gateway** :
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Você verá a página **Gateway** :   

        ![Gateway com modo de exibição de nó único](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Clique em **adicionar nó** na barra de ferramentas para adicionar um nó ao gateway lógico. Se você estiver planejando usar a instalação expressa, siga esta etapa do computador local que será adicionado como um nó ao gateway. 

    ![Gerenciamento de Dados gateway – adicionar menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. As etapas são semelhantes à configuração do primeiro nó. A interface do usuário do Configuration Manager permite definir o nome do nó se você escolher a opção de instalação manual: 

    ![Configuration Manager-instalar o segundo gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Depois que o gateway for instalado com êxito no nó, a ferramenta Configuration Manager exibirá a tela a seguir:  

    ![Configuration Manager-instalar o segundo gateway com êxito](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se você abrir a página **Gateway** no portal, verá dois nós de gateway agora: 

    ![Gateway com dois nós no portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para excluir um nó de gateway, clique em **excluir nó** na barra de ferramentas, selecione o nó que você deseja excluir e, em seguida, clique em **excluir** na barra de ferramentas. Essa ação exclui o nó selecionado do grupo. Observe que essa ação não desinstala o software do gateway de gerenciamento de dados do nó (computador Windows local). Use **Adicionar ou remover programas** no painel de controle no local para desinstalar o gateway. Quando você desinstala o gateway do nó, ele é excluído automaticamente no Portal.   

## <a name="upgrade-an-existing-gateway"></a>Atualizar um gateway existente
Você pode atualizar um gateway existente para usar o recurso de alta disponibilidade e escalabilidade. Esse recurso funciona apenas com nós que têm o gateway de gerenciamento de dados da versão > = 2.12. xxxx. Você pode ver a versão do gateway de gerenciamento de dados instalada em um computador na guia **ajuda** do Configuration Manager do gateway de gerenciamento de dados. 

1. Atualize o gateway no computador local para a versão mais recente seguindo baixando e executando um pacote de instalação MSI do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Consulte a seção [instalação](data-factory-data-management-gateway.md#installation) para obter detalhes.  
2. Navegue até a portal do Azure. Inicie a **página de data Factory** para seu data Factory. Clique no bloco serviços vinculados para iniciar a **página serviços vinculados**. Selecione o gateway para iniciar a **página do gateway**. Clique e habilite o **recurso de visualização** conforme mostrado na imagem a seguir: 

    ![Gerenciamento de Dados gateway – habilitar recurso de visualização](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Depois que o recurso de visualização estiver habilitado no portal, feche todas as páginas. Reabra a **página gateway** para ver a nova interface do usuário de visualização (IU).
 
    ![Gerenciamento de Dados gateway-habilitar o êxito do recurso de visualização](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Gerenciamento de Dados gateway-versão prévia da interface do usuário](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante a atualização, o nome do primeiro nó é o nome do computador. 
3. Agora, adicione um nó. Na página **Gateway** , clique em **adicionar nó**.  

    ![Gerenciamento de Dados gateway – adicionar menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga as instruções da seção anterior para configurar o nó. 

### <a name="installation-best-practices"></a>Práticas recomendadas de instalação

- Configure o plano de energia no computador host para o gateway para que o computador não faça hibernação. Se o computador host hibernar, o gateway não responderá às solicitações de dados.
- Faça backup do certificado associado ao gateway.
- Verifique se todos os nós são de configuração semelhante (recomendado) para o desempenho ideal. 
- Adicione pelo menos dois nós para garantir a alta disponibilidade.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
Aqui estão os requisitos para o certificado TLS/SSL que é usado para proteger as comunicações entre nós do tempo de execução de integração:

- O certificado deve ser um certificado X509 v3 publicamente confiável. Recomendamos que você use certificados emitidos por uma autoridade de certificação (CA) pública (de terceiros).
- Cada nó do Integration Runtime deve confiar nesse certificado, bem como no computador cliente que está executando o aplicativo do Credential Manager. 
  > [!NOTE]
  > O aplicativo Gerenciador de credenciais é usado enquanto configura com segurança a credencial do assistente de cópia/portal do Azure. E isso pode ser acionado de qualquer computador na mesma rede que o armazenamento de dados local/privado.
- Há suporte para certificados curinga. Se o nome do FQDN for **Node1.domain.contoso.com**, você poderá usar * **. domain.contoso.com** como o nome da entidade do certificado.
- Os certificados de SAN não são recomendados, pois somente o último item dos nomes alternativos da entidade será usado e todos os outros serão ignorados devido à limitação atual. Por exemplo, Você tem um certificado SAN cuja SAN é **Node1.domain.contoso.com** e **NODE2.domain.contoso.com**, você só pode usar esse certificado no computador cujo FQDN é **NODE2.domain.contoso.com**.
- Dá suporte a qualquer tamanho de chave com suporte do Windows Server 2012 R2 para certificados SSL.
- Não há suporte para o certificado usando chaves CNG.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Perguntas frequentes: quando não habilitar essa criptografia?
A habilitação da criptografia pode adicionar determinado custo à sua infraestrutura (proprietário do certificado público), portanto, você pode ignorar a habilitação da criptografia nos casos abaixo:
- Quando o Integration Runtime está em execução em uma rede confiável ou em uma rede com criptografia transparente, como IP/s. Como essa comunicação de canal só é limitada em sua rede confiável, talvez você não precise de criptografia adicional.
- Quando o Integration Runtime não está em execução em um ambiente de produção. Isso pode ajudar a reduzir o custo do certificado TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorar um gateway de vários nós
### <a name="multi-node-gateway-monitoring"></a>Monitoramento de gateway de vários nós
No portal do Azure, você pode exibir o instantâneo quase em tempo real da utilização de recursos (CPU, memória, rede (entrada/saída), etc.) em cada nó junto com os status dos nós de gateway. 

![Gateway de Gerenciamento de Dados-monitoramento de vários nós](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Você pode habilitar **as configurações avançadas** na página **Gateway** para ver as métricas avançadas como **rede**(entrada/saída), **função & status da credencial**, o que é útil na depuração de problemas de gateway e **trabalhos simultâneos** (em execução/limite ) que pode ser modificado/alterado de acordo durante o ajuste de desempenho. A tabela a seguir fornece descrições das colunas na lista **nós de gateway** :  

Propriedade de monitoramento | Descrição
:------------------ | :---------- 
Nome | Nome do gateway lógico e nós associados ao gateway.  
Estado | Status do gateway lógico e dos nós de gateway. Exemplo: online/offline/Limited/etc. Para obter informações sobre esses status, consulte a seção [status do gateway](#gateway-status) . 
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão da maioria dos nós no grupo. Se houver nós com versões diferentes na configuração do gateway lógico, somente os nós com o mesmo número de versão que o gateway lógico funcionarão corretamente. Outras estão no modo limitado e precisam ser atualizadas manualmente (somente em caso de falha de atualização automática). 
Memória disponível | Memória disponível em um nó de gateway. Esse valor é um instantâneo quase em tempo real. 
Utilização da CPU | Utilização da CPU de um nó de gateway. Esse valor é um instantâneo quase em tempo real. 
Rede (entrada/saída) | Utilização de rede de um nó de gateway. Esse valor é um instantâneo quase em tempo real. 
Trabalhos simultâneos (em execução/limite) | Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. Limit significa o máximo de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho da máquina. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, em que a CPU/memória/rede está subutilizada, mas as atividades estão atingindo o tempo limite. Esse recurso também está disponível com um gateway de nó único (mesmo quando o recurso de escalabilidade e disponibilidade não está habilitado). Para obter mais informações, consulte a seção [Considerações sobre escala](#scale-considerations) . 
Função | Há dois tipos de funções – Dispatcher e Worker. Todos os nós são trabalhadores, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó Dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e expedir-os para diferentes nós de trabalho (incluindo ele mesmo). 

![Gateway de Gerenciamento de Dados-monitoramento avançado de vários nós](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Status do gateway

A tabela a seguir fornece os possíveis status de um **nó de gateway**: 

Estado  | Comentários/cenários
:------- | :------------------
Online | Nó conectado ao serviço de Data Factory.
Está | O nó está offline.
Upgrade | O nó está sendo atualizado automaticamente.
Limitado | Devido a um problema de conectividade. Pode ser devido ao problema de porta HTTP 8050, problema de conectividade do barramento de serviço ou problema de sincronização de credenciais. 
Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria.<br/><br/> Um nó pode ficar inativo quando não puder se conectar a outros nós. 


A tabela a seguir fornece os possíveis status de um **Gateway lógico**. O status do gateway depende dos status dos nós do gateway. 

Estado | Comentários
:----- | :-------
Precisa de registro | Nenhum nó ainda está registrado para este gateway lógico
Online | Os nós de gateway estão online
Está | Nenhum nó no status online.
Limitado | Nem todos os nós neste gateway estão em estado íntegro. Esse status é um aviso de que algum nó pode estar inoperante! <br/><br/>Pode ser devido a um problema de sincronização de credenciais no nó Dispatcher/de trabalho. 

### <a name="pipeline-activities-monitoring"></a>Monitoramento de atividades/pipeline
O portal do Azure fornece uma experiência de monitoramento de pipeline com detalhes de nível de nó granular. Por exemplo, ele mostra quais atividades foram executadas em qual nó. Essas informações podem ser úteis para entender problemas de desempenho em um nó específico, por exemplo, devido à limitação da rede. 

![Gateway de Gerenciamento de Dados-monitoramento de vários nós para pipelines](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Gerenciamento de Dados gateway-detalhes do pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerações sobre escala

### <a name="scale-out"></a>Aumentar horizontalmente
Quando a **memória disponível está baixa** e o **uso da CPU é alto**, a adição de um novo nó ajuda a escalar horizontalmente a carga entre as máquinas. Se as atividades estiverem falhando devido ao tempo limite ou ao nó de gateway estar offline, isso ajudará se você adicionar um nó ao gateway.
 
### <a name="scale-up"></a>Aumentar verticalmente
Quando a memória disponível e a CPU não são utilizadas bem, mas a capacidade ociosa é 0, você deve escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó. Você também pode querer escalar verticalmente quando as atividades esgotam o tempo limite porque o gateway está sobrecarregado. Conforme mostrado na imagem a seguir, você pode aumentar a capacidade máxima de um nó. Sugerimos dobrar para começar.  

![Gerenciamento de Dados considerações de escala de gateway](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Problemas conhecidos/alterações significativas

- No momento, você pode ter até quatro nós de gateway físico para um único gateway lógico. Se você precisar de mais de quatro nós por motivos de desempenho, envie um email para [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com).
- Você não pode registrar novamente um nó de gateway com a chave de autenticação de outro gateway lógico para alternar do gateway lógico atual. Para registrar novamente, desinstale o gateway do nó, reinstale o gateway e registre-o com a chave de autenticação para o outro gateway lógico. 
- Se o proxy HTTP for necessário para todos os seus nós de gateway, defina o proxy em diahost. exe. config e diawp. exe. config e use o Gerenciador de servidores para verificar se todos os nós têm o mesmo diahost. exe. config e diawip. exe. config. Consulte a seção [definir configurações de proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) para obter detalhes. 
- Para alterar o modo de criptografia para comunicação de nó para nó no gateway Configuration Manager, exclua todos os nós no portal, exceto um. Em seguida, adicione nós de volta depois de alterar o modo de criptografia.
- Use um certificado SSL oficial se você optar por criptografar o canal de comunicação de nó para nó. O certificado autoassinado pode causar problemas de conectividade, pois o mesmo certificado pode não ser confiável na lista de autoridades de certificação em outros computadores. 
- Não é possível registrar um nó de gateway em um gateway lógico quando a versão do nó é menor do que a versão do gateway lógico. Exclua todos os nós do gateway lógico do portal para que você possa registrar um nó de versão inferior (downgrade). Se você excluir todos os nós de um gateway lógico, instale e Registre manualmente novos nós no gateway lógico. Neste caso, não há suporte para a instalação expressa.
- Não é possível usar a instalação expressa para instalar nós em um gateway lógico existente, que ainda está usando credenciais de nuvem. Você pode verificar onde as credenciais são armazenadas do gateway Configuration Manager na guia Configurações.
- Não é possível usar a instalação expressa para instalar nós em um gateway lógico existente, que tem a criptografia de nó para nó habilitada. Como a definição do modo de criptografia envolve a adição manual de certificados, a instalação expressa não é mais uma opção. 
- Para uma cópia de arquivo do ambiente local, você não deve usar \\localhost ou C:\files mais uma vez que a unidade local ou localhost pode não estar acessível por todos os nós. Em vez disso, use \\ServerName\files para especificar o local dos arquivos.


## <a name="rolling-back-from-the-preview"></a>Revertendo da visualização 
Para reverter da versão prévia, exclua todos os nós, mas um nó. Não importa quais nós você exclui, mas certifique-se de ter pelo menos um nó no gateway lógico. Você pode excluir um nó desinstalando o gateway no computador ou usando o portal do Azure. Na portal do Azure, na página **Data Factory** , clique em serviços vinculados para iniciar a página **Serviços vinculados** . Selecione o gateway para iniciar a página do **Gateway** . Na página gateway, você pode ver os nós associados ao gateway. A página permite que você exclua um nó do gateway.
 
Após a exclusão, clique em **recursos de visualização** na mesma página portal do Azure e desabilite o recurso de visualização. Você redefiniu o gateway para o gateway GA (disponibilidade geral) de um nó.


## <a name="next-steps"></a>Passos seguintes
Examine os seguintes artigos:
- [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) – fornece uma visão geral detalhada do gateway.
- [Mover dados entre armazenamentos de dados locais e em nuvem](data-factory-move-data-between-onprem-and-cloud.md) -contém um passo a passos com instruções passo a passo para usar um gateway com um único nó. 
