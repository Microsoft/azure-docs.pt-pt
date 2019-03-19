---
title: Elevada disponibilidade com o data management gateway na fábrica de dados do Azure | Documentos da Microsoft
description: Este artigo explica como pode aumentar horizontalmente um gateway de gestão de dados ao adicionar mais nós e dimensionamento aumente o número de tarefas simultâneas que podem ser executadas num nó a cópia de segurança.
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
ms.openlocfilehash: 08e7341bfd1c384e41e6d3f1bd7810552899849a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092196"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Gateway de gestão de dados - elevada disponibilidade e escalabilidade (pré-visualização)
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [Self-hosted integration runtime no](../create-self-hosted-integration-runtime.md). 


Este artigo ajuda-o a configurar a solução de elevada disponibilidade e escalabilidade com o Gateway de gestão de dados / integração.    

> [!NOTE]
> Este artigo pressupõe que já esteja familiarizado com noções básicas do Integration Runtime (Data Management Gateway anterior). Se não for, veja [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **Esta funcionalidade de pré-visualização é oficialmente suportada no 2.12.xxxx.x de versão do Gateway de gestão de dados e superior**. Certifique-se de que está a utilizar versão 2.12.xxxx.x ou superior. Baixe a versão mais recente do Data Management Gateway [aqui](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Descrição geral
Pode associar os gateways de gestão de dados que são instalados em várias máquinas no local com um único gateway lógico no portal. Estas máquinas são chamadas **nós**. Pode ter até **quatro nós** associados a um gateway lógico. Os benefícios de ter vários nós (máquinas no local com o gateway instalado) para um gateway lógico são:  

- Melhorar o desempenho de movimento de dados entre aplicações no local e na cloud arquivos de dados.  
- Se um de nós ficar inativo por algum motivo, outros nós ainda estão disponíveis para mover os dados. 
- Se um de nós tem de ser colocado offline para manutenção, outros nós ainda estão disponíveis para mover os dados.

Também pode configurar o número de **tarefas de movimento de dados em simultâneo** que podem ser executados num nó para aumentar verticalmente a capacidade de mover dados entre aplicações no local e na cloud com arquivos de dados. 

Utilizar o portal do Azure, pode monitorizar o estado de em nós, que lhe permite decidir se pretende adicionar ou remover um nó de gateway lógico. 

## <a name="architecture"></a>Arquitetura 
O diagrama seguinte apresenta a visão geral da arquitetura de escalabilidade e a funcionalidade de disponibilidade do Gateway de gestão de dados: 

![Gateway de gestão de dados - elevada disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

R **gateway lógico** é o gateway adiciona a uma fábrica de dados no portal do Azure. Anteriormente, podia associar apenas uma máquina de Windows no local com o Data Management Gateway instalado com um gateway lógico. Isso-local no computador gateway denomina-se um nó. Agora, pode associar até **quatro nós físicos** com um gateway lógico. Um gateway lógico com vários nós é chamado um **gateway de vários nó**.  

Todos estes nós fazem **Active Directory**. Todos eles podem processar tarefas de movimento de dados para mover dados entre aplicações no local e na cloud arquivos de dados. Um de nós atuam como o dispatcher e de trabalho. Outros nós nos grupos são nós de trabalho. R **dispatcher** nó extrai dados movimento tarefas/trabalhos a partir do serviço em nuvem e expede para nós de trabalho (incluindo ele próprio). R **trabalho** nó executa as tarefas de movimento de dados para mover dados entre aplicações no local e na cloud arquivos de dados. Todos os nós são trabalhadores. Apenas um nó pode ser expedição e de trabalho.    

Normalmente, pode começar com um nó e **aumentar horizontalmente** para adicionar mais nós à medida que o nó existente (s) assoberbados com a carga de movimento de dados. Também pode **aumentar verticalmente** a capacidade de movimento de dados de um nó de gateway do aumento do número de tarefas simultâneas que podem ser executados no nó. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). 

Um gateway com vários nós mantém as credenciais de arquivo de dados sincronizados em todos os nós. Se houver um problema de conectividade de nó para nó, as credenciais podem ser fora de sincronia. Quando definir as credenciais para um arquivo de dados no local que utiliza um gateway, guarda as credenciais no nó do distribuidor/trabalhador. As sincronizações de nó de dispatcher com outros nós de trabalho. Este processo é conhecido como **sincronização de credenciais**. O canal de comunicação entre os nós pode ser **encriptados** por um certificado SSL/TLS público. 

## <a name="set-up-a-multi-node-gateway"></a>Configurar um gateway com vários nó
Esta secção assume que já leu os seguintes dois artigos ou estiver familiarizado com conceitos nestes artigos: 

- [O Data Management Gateway](data-factory-data-management-gateway.md) -fornece uma visão geral detalhada do gateway.
- [Mover dados entre locais e na cloud arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) -contém um passo a passo com instruções passo a passo para utilizar um gateway com um único nó.  

> [!NOTE]
> Antes de instalar um gateway de gestão de dados numa máquina Windows no local, consulte os pré-requisitos listados [o artigo principal](data-factory-data-management-gateway.md#prerequisites).

1. Na [passo a passo](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), ao criar um gateway lógico, ativar a **elevada disponibilidade e escalabilidade** funcionalidade. 

    ![Gateway de gestão de dados - ativar a elevada disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na **configurar** página, a utilização de uma **configuração rápida** ou **configuração Manual** link para instalar um gateway no primeiro nó (uma máquina do Windows no local).

    ![Gateway de gestão de dados - configuração expressa ou manual](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se utilizar a opção de configuração rápida, a comunicação de nó para nó é realizada sem encriptação. O nome do nó é igual ao nome da máquina. Utilize a configuração manual se a comunicação nó a nó tem de ser encriptados ou se pretender especificar um nome de nó da sua preferência. Nomes de nó não não possível editar mais tarde.
3. Se escolher **configuração rápida**
    1. Verá a seguinte mensagem depois do gateway foi instalado com êxito:

        ![Gateway de gestão de dados - êxito da configuração rápida](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Inicie o Gestor de configuração de gestão de dados para o gateway ao seguir [estas instruções](data-factory-data-management-gateway.md#configuration-manager). Verá o nome do gateway, o nome do nó, estado, etc.

        ![Gateway de gestão de dados - instalação concluída com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se escolher **configuração manual**:
    1. Transferir o pacote de instalação do Microsoft Download Center, execute-o para instalar o gateway no seu computador.
    2. Utilize o **chave de autenticação** partir a **configurar** página para registar o gateway.
    
        ![Gateway de gestão de dados - instalação concluída com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na **novo nó de gateway** página, pode fornecer um personalizado **nome** para o nó de gateway. Por predefinição, o nome do nó é igual ao nome da máquina.    

        ![Gateway de gestão de dados - especifique o nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na página seguinte, pode escolher se pretende **ativar a encriptação para a comunicação de nó para nó**. Clique em **ignorar** para desativar a encriptação (predefinição).

        ![Gateway de gestão de dados - ativar a encriptação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Só é suportada a alteração do modo de encriptação quando tem um nó de gateway único no gateway lógico. Para alterar o modo de encriptação quando vários nós de tem um gateway, siga os passos abaixo: eliminar todos os nós, exceto um nó, alterar o modo de encriptação e, em seguida, adicionar novamente os nós.
        > 
        > Ver [requisitos de certificado TLS/SSL](#tlsssl-certificate-requirements) secção para obter uma lista de requisitos para utilizar um certificado TLS/SSL. 
    5. Depois do gateway foi instalado com êxito, clique em Iniciar Configuration Manager:
    
        ![Configuração manual - Iniciar configuration manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. consulte o Gestor de configuração de Gateway de gestão de dados no nó (máquina do Windows no local), que mostra o estado de conectividade, **nome do gateway**, e **nome do nó**.  

        ![Gateway de gestão de dados - instalação concluída com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se estiver a aprovisionar o gateway numa VM do Azure, pode utilizar [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Este script cria um gateway lógico, configura a VMs com o software de Data Management Gateway instalado e regista-os com o gateway lógico. 
6. No portal do Azure, inicie o **Gateway** página: 
    1. Na página inicial de data factory no portal, clique em **serviços ligados**.
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Selecione o **gateway** para ver a **Gateway** página:
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Verá o **Gateway** página:   

        ![Gateway com a vista de nó único](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Clique em **adicionar nó** na barra de ferramentas para adicionar um nó para o gateway lógico. Se estiver a planear utilizar a configuração rápida, execute este passo da máquina no local que será adicionada como um nó para o gateway. 

    ![Gateway de gestão de dados - adicionar o menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Passos são semelhantes para configurar o primeiro nó. A IU do Gestor de configuração permite-lhe definir o nome do nó, se escolher a opção de instalação manual: 

    ![Configuration Manager - instalação segundo gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Depois do gateway é instalado com êxito no nó, a ferramenta Configuration Manager apresenta o ecrã seguinte:  

    ![Configuration Manager - instalação segundo gateway com êxito](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se abrir o **Gateway** página no portal, verá dois nós de gateway agora: 

    ![Gateway com dois nós no portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para eliminar um nó de gateway, clique em **eliminar nó** na barra de ferramentas, selecione o nó que pretende eliminar e, em seguida, clique em **eliminar** da barra de ferramentas. Esta ação elimina o nó selecionado do grupo. Tenha em atenção que esta ação não desinstala o software de gateway de gestão de dados do nó (máquina do Windows no local). Uso **adicionar ou remover programas** no painel de controlo no local para desinstalar o gateway. Quando desinstalar o gateway a partir do nó, é eliminado automaticamente no portal.   

## <a name="upgrade-an-existing-gateway"></a>Atualizar um gateway existente
Pode atualizar um gateway existente para utilizar a funcionalidade de elevada disponibilidade e escalabilidade. Esta funcionalidade funciona apenas connosco que têm o gateway de gestão de dados da versão > = 2.12.xxxx. Pode ver a versão do gateway de gestão de dados instalada numa máquina no **ajudar** separador do Data Management Gateway Configuration Manager. 

1. Atualizar o gateway na máquina no local para a versão mais recente pelo seguinte ao transferir e executar um MSI de configuração pacote a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Ver [instalação](data-factory-data-management-gateway.md#installation) secção para obter detalhes.  
2. Navegue para o portal do Azure. Iniciar o **página da fábrica de dados** fábrica de dados. Clique no mosaico serviços ligados para iniciar o **página de serviços ligados**. Selecione o gateway para iniciar o **página do gateway**. Clique e ativar **funcionalidade de pré-visualização** conforme mostrado na imagem seguinte: 

    ![Gateway de gestão de dados - funcionalidade de pré-visualização de ativação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Assim que a funcionalidade de pré-visualização está ativada no portal, feche todas as páginas. Reabra o **página do gateway** para ver a nova interface de utilizador de pré-visualização (IU).
 
    ![Gateway de gestão de dados - êxito de funcionalidade de pré-visualização ativar](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Gateway de gestão de dados - pré-visualização da interface do Usuário](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante a atualização, o nome do nó primeiro é o nome da máquina. 
3. Agora, adicione um nó. Na **Gateway** página, clique em **adicionar nó**.  

    ![Gateway de gestão de dados - adicionar o menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga as instruções da secção anterior para configurar o nó. 

### <a name="installation-best-practices"></a>Melhores práticas de instalação

- Configure o esquema de energia no computador anfitrião para o gateway para que a máquina sem a hibernação. Se a máquina de anfitrião hiberna, o gateway não responde a pedidos de dados.
- Criar cópias de segurança o certificado associado ao gateway.
- Certifique-se de que todos os nós são de configuração semelhante (recomendada) para um desempenho ideal. 
- Adicione, pelo menos, dois nós para garantir a elevada disponibilidade.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
Eis os requisitos para o certificado TLS/SSL que é utilizado para proteger as comunicações entre o integration runtime nós:

- O certificado tem de ser um publicamente fidedigno X509 v3 certificado. Recomendamos que utilize certificados emitidos por uma autoridade de certificação de (de terceiros) pública (AC).
- Cada nó do integration runtime têm de confiar este certificado, bem como a máquina de cliente que está a executar a aplicação do Gestor de credenciais. 
  > [!NOTE]
  > Aplicação do Gestor de credencial é utilizada ao definir com segurança credencial a partir do Assistente de cópia / Portal do Azure. E isso pode ser acionada a partir de qualquer máquina dentro da mesma rede como no local / privada arquivo de dados.
- Os certificados de caráter universal são suportados. Se o nome FQDN for **node1.domain.contoso.com**, pode utilizar ***. domain.contoso.com** como nome do requerente do certificado.
- Certificados SAN não são recomendados uma vez que será utilizado apenas o último item dos nomes alternativos do requerente e todos os outros serão ignorados devido a limitação atual. Por exemplo, tem um certificado SAN é cujo SAN **node1.domain.contoso.com** e **node2.domain.contoso.com**, só pode utilizar este certificado no computador cujo FQDN for **node2.domain.contoso.com**.
- Suporta qualquer tamanho de chave suportado pelo Windows Server 2012 R2 para certificados SSL.
- Usando a CNG de certificado não são suportadas chaves.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>FAQ: Quando eu não permitiria esta encriptação?
Ativar a encriptação pode adicionar determinados custos à sua infra-estrutura (proprietário certificado público), por conseguinte, pode ignorar a ativação da encriptação no abaixo casos:
- Quando o integration runtime está em execução numa rede fidedigna ou uma rede com a encriptação transparente, como o IP/seg. Uma vez que esta comunicação de canal é apenas limitada dentro da rede fidedigna, talvez não seja necessária encriptação adicional.
- Quando o integration runtime não está em execução num ambiente de produção. Isto pode ajudar a reduzir o custo de certificado TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorizar um gateway com vários nó
### <a name="multi-node-gateway-monitoring"></a>Monitorização de vários nó gateway
No portal do Azure, pode ver o instantâneo de quase em tempo real de utilização de recursos (CPU, memória, network(in/out), etc.) em cada nó, juntamente com os Estados de nós de gateway. 

![Gateway de gestão de dados - monitorização de nó vários](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Pode habilitar **definições avançadas** no **Gateway** página para ver métricas avançadas, como **rede**(de entrada/saída), **função & Estado da credencial**, que é útil na depuração de problemas do gateway, e **tarefas simultâneas** (em execução / limite) que pode ser modificado / adequadamente alterado durante a otimização do desempenho. A tabela seguinte fornece descrições das colunas na **nós de Gateway** lista:  

Propriedade de monitorização | Descrição
:------------------ | :---------- 
Name | Nome do gateway lógico e nós associado ao gateway.  
Estado | Estado do gateway lógico e os nós de gateway. Exemplo: Online/Offline/limitado/etc. Para obter informações sobre estes Estados terem, consulte [estado do Gateway](#gateway-status) secção. 
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão da maioria de nós no grupo. Se existirem nós com versões diferentes na configuração de gateway lógico, apenas os nós com o mesmo número de versão e a função de gateway lógico corretamente. Outras pessoas estão no modo de limitada e têm de ser atualizados manualmente (apenas no caso de falha de atualização automática). 
Memória disponível | Memória disponível num nó de gateway. Este valor é um instantâneo de quase em tempo real. 
Utilização da CPU | Utilização da CPU de um nó de gateway. Este valor é um instantâneo de quase em tempo real. 
Funcionamento em rede (entrada/saída) | Utilização de um nó de gateway de rede. Este valor é um instantâneo de quase em tempo real. 
Tarefas simultâneas (em execução / limite) | Número de tarefas ou tarefas em execução em cada nó. Este valor é um instantâneo de quase em tempo real. Limite significa o máximo de tarefas simultâneas para cada nó. Este valor é definido com base no tamanho de máquina. Pode aumentar o limite para aumentar verticalmente a execução da tarefa em simultâneo em cenários avançados, onde CPU / memória / rede é subutilizados, mas atividades são exceder o tempo limite. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). Para obter mais informações, consulte [Dimensionar considerações](#scale-considerations) secção. 
Função | Existem dois tipos de funções – Dispatcher e de trabalho. Todos os nós são funções de trabalho, o que significa que eles podem todos ser usados para executar tarefas. Há apenas um nó de dispatcher, que é utilizado para extrair tarefas/tarefas dos serviços cloud e expedi-los para nós de trabalho diferentes (incluindo ele próprio). 

![Gateway de gestão de dados - vários de nó monitorização avançado](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Estado do gateway

A tabela seguinte fornece os Estados possíveis de uma **nó de gateway**: 

Estado  | Comentários/cenários
:------- | :------------------
Online | O nó ligado ao serviço Data Factory.
Offline | O nó está offline.
A atualizar | O nó está a ser atualizados automaticamente.
Limitado | Devido a problema de conectividade. Pode ser devido a problema de 8050 de porta HTTP, o problema de conectividade de barramento de serviço ou o problema de sincronização de credenciais. 
Inativa | O nó está numa configuração diferente da configuração dos outros nós da maioria.<br/><br/> Um nó pode ficar inativo, quando ele não é possível ligar a outros nós. 


A tabela seguinte fornece os Estados possíveis de uma **gateway lógico**. O estado do gateway depende de Estados de nós de gateway. 

Estado | Comentários
:----- | :-------
Tem de ser registado | Nenhum nó ainda está registada para este gateway lógico
Online | Nós de gateway estão online
Offline | Nenhum nó no estado online.
Limitado | Nem todos os nós neste gateway estão em bom estado de funcionamento. Este estado é um aviso de que alguns nós podem estar inativa! <br/><br/>Pode ser devido a problema de sincronização de credenciais no nó de distribuidor/trabalhador. 

### <a name="pipeline-activities-monitoring"></a>Pipeline / monitorização de atividades
O portal do Azure fornece uma experiência com os detalhes de nível granular de nó de monitorização de pipeline. Por exemplo, mostra que atividades foram executadas no nó que. Esta informação pode ser útil na compreensão de problemas de desempenho num determinado nó, digamos que devido à limitação de rede. 

![Gateway de gestão de dados - monitorização para pipelines de vários nós em](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Gateway de gestão de dados - detalhes de pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerações de dimensionamento

### <a name="scale-out"></a>Aumentar horizontalmente
Quando o **memória disponível é baixa** e o **utilização da CPU é alta**, adicionar um novo nó ajuda a aumentar horizontalmente a carga entre máquinas. Se atividades estão a falhar devido ao nó de limite de tempo ou o gateway estar offline, isso será útil se adicionar um nó para o gateway.
 
### <a name="scale-up"></a>Aumentar verticalmente
Quando a memória disponível e a CPU não são utilizados bem, mas a capacidade inativa é 0, deve aumentar verticalmente ao aumento do número de tarefas simultâneas que podem ser executadas num nó. Pode também querer aumentar verticalmente quando as atividades são exceder o tempo limite porque o gateway está sobrecarregado. Conforme mostrado na imagem seguinte, pode aumentar a capacidade máxima para um nó. Sugerimos que dobra-lo para começar.  

![Gateway de gestão de dados - considerações de dimensionamento](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Alterações recentes/problemas conhecidos

- Atualmente, pode ter até quatro nós de gateway físico para um único gateway lógico. Se precisar de mais de quatro nós por motivos de desempenho, envie um e-mail para [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Não é possível voltar a registar um nó de gateway com a chave de autenticação a partir de outro gateway lógico para alternar entre o gateway lógico atual. Para voltar a registar, desinstalar o gateway a partir do nó, em seguida, reinstale o gateway e registá-lo com a chave de autenticação para o gateway de lógica. 
- Se o proxy HTTP é necessário para todos os nós de gateway, definiu o proxy no diahost.exe.config e diawp.exe.config e utilize o Gestor de servidor para certificar-se de que todos os nós têm a mesma diahost.exe.config e diawip.exe.config. Ver [configurar definições de proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) secção para obter detalhes. 
- Para alterar o modo de encriptação para a comunicação de nó para nó no Gestor de configuração do Gateway, elimine todos os nós no portal, exceto um. Em seguida, adicione nós novamente depois de alterar o modo de encriptação.
- Utilize um certificado SSL oficial se optar por encriptar o canal de comunicação de nó para nó. Certificado autoassinado pode causar problemas de conectividade, como o mesmo certificado poderá não ser fidedigna na lista de autoridades de certificação em outras máquinas. 
- Não é possível registar um nó de gateway para um gateway lógico quando a versão do nó é inferior à versão do gateway lógico. Eliminar todos os nós do gateway lógico a partir do portal para que pode registrar um node(downgrade) versão inferior-lo. Se eliminar todos os nós de um gateway lógico, manualmente instalar e registar novos nós para esse gateway lógico. Configuração rápida não é suportada neste caso.
- Não é possível utilizar a configuração rápida para instalar nós a um gateway lógico existente, o que ainda está a utilizar as credenciais da cloud. Pode verificar em que as credenciais são armazenadas do Gestor de configuração do Gateway no separador Definições.
- Não é possível utilizar a configuração rápida para instalar nós a um gateway lógico existente, tem a encriptação de nó para nó ativada. Como definir o modo de encriptação envolve a adição manual de certificados, instalação expressa do opção não está mais uma. 
- Para obter uma cópia do ficheiro de ambiente no local, não deve utilizar \\localhost ou C:\files deixa de poder desde localhost ou uma unidade local pode não estar acessível através de todos os nós. Em alternativa, utilize \\ServerName\files para especificar a localização dos ficheiros.


## <a name="rolling-back-from-the-preview"></a>A reverter a partir da pré-visualização do 
Para reverter a partir da pré-visualização do, elimine todos os nós, mas um nó. Não importa que nós, eliminar, mas certifique-se de que tem pelo menos um nó no gateway lógico. Pode eliminar um nó, desinstalando o gateway no computador ou ao utilizar o portal do Azure. No portal do Azure, no **Data Factory** página, clique em serviços ligados para iniciar o **serviços ligados** página. Selecione o gateway para iniciar o **Gateway** página. Na página do Gateway, pode ver os nós associado ao gateway. A página permite que exclua um nó a partir do gateway.
 
Depois de eliminar, clique em **funcionalidades de pré-visualização** na mesma página do portal do Azure e desativar a funcionalidade de pré-visualização. Tem de repor o gateway para um gateway de (disponibilidade geral) de disponibilidade geral de nó.


## <a name="next-steps"></a>Passos Seguintes
Reveja os artigos seguintes:
- [O Data Management Gateway](data-factory-data-management-gateway.md) -fornece uma visão geral detalhada do gateway.
- [Mover dados entre locais e na cloud arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) -contém um passo a passo com instruções passo a passo para utilizar um gateway com um único nó. 
