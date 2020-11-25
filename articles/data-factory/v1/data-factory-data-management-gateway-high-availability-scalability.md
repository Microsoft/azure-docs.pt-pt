---
title: Alta disponibilidade com porta de gestão de dados na Azure Data Factory
description: Este artigo explica como se pode escalar uma porta de entrada de gestão de dados adicionando mais nós e aumentando o número de empregos simultâneos que podem funcionar num nó.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b8d05293359cff16bb6d8c9a629a1fbf68104365
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003621"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - alta disponibilidade e escalabilidade (Pré-visualização)
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [tempo de integração auto-hospedado em](../create-self-hosted-integration-runtime.md). 


Este artigo ajuda-o a configurar uma solução de alta disponibilidade e escalabilidade com a Data Management Gateway/ Integração.    

> [!NOTE]
> Este artigo assume que já está familiarizado com os fundamentos do Tempo de Execução de Integração (Gateway de Gestão de Dados anterior). Se não estiver, consulte o [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **Esta funcionalidade de pré-visualização é oficialmente suportada na versão 2.12.xxxx.x e acima**. Certifique-se de que está a utilizar a versão 2.12.xxxx.x ou acima. Descarregue [aqui](https://www.microsoft.com/download/details.aspx?id=39717)a versão mais recente do Data Management Gateway .

## <a name="overview"></a>Descrição geral
Pode associar gateways de gestão de dados que são instalados em várias máquinas no local com um único portal lógico. Estas máquinas são chamadas **nós.** Pode ter até **quatro nós associados** a uma porta lógica. Os benefícios de ter múltiplos nós (máquinas no local com gateway instalado) para um gateway lógico são:  

- Melhorar o desempenho do movimento de dados entre as lojas de dados no local e as lojas de dados em nuvem.  
- Se um dos nós descer por alguma razão, outros nós ainda estão disponíveis para mover os dados. 
- Se um dos nós precisar de ser desligado para manutenção, outros nós ainda estão disponíveis para mover os dados.

Também pode configurar o número de **trabalhos simultâneos** de movimento de dados que podem funcionar num nó para aumentar a capacidade de mover dados entre as lojas de dados no local e na nuvem. 

Utilizando o portal Azure, pode monitorizar o estado destes nós, o que o ajuda a decidir se adiciona ou remove um nó do gateway lógico. 

## <a name="architecture"></a>Arquitetura 
O diagrama a seguir fornece a visão geral da arquitetura da escalabilidade e característica de disponibilidade do Gateway de Gestão de Dados: 

![Gateway de Gestão de Dados - Alta Disponibilidade e Escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Um **portal lógico** é a porta de entrada que se adiciona a uma fábrica de dados no portal Azure. Anteriormente, poderia associar apenas uma máquina windows no local com o Data Management Gateway instalado com um gateway lógico. Esta máquina de gateway no local é chamada de nó. Agora, pode associar até **quatro nós físicos** a uma porta lógica. Uma porta de entrada lógica com múltiplos nós é chamada de **porta de entrada multi-nós.**  

Todos estes nós estão **ativos.** Todos eles podem processar trabalhos de movimento de dados para mover dados entre as lojas de dados no local e as lojas de dados em nuvem. Um dos nós atua como despachante e trabalhador. Outros nós nos grupos são nós operários. Um nó **de despacho** retira tarefas/trabalhos de movimento de dados do serviço de nuvem e envia-os para nós de trabalhadores (incluindo a si mesmo). Um nó **de trabalhador** executa trabalhos de movimento de dados para mover dados entre as lojas de dados no local e as lojas de dados em nuvem. Todos os nós são trabalhadores. Só um nó pode ser despachando e trabalhador.    

Normalmente, pode começar com um nó e escalar para **adicionar** mais nós, uma vez que os nós existentes estão sobrecarregados com a carga de movimento de dados. Também pode **aumentar** a capacidade de movimento de dados de um nó de gateway aumentando o número de empregos simultâneos que são permitidos funcionar no nó. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). 

Um gateway com múltiplos nós mantém as credenciais de armazenamento de dados em sincronização em todos os nós. Se houver um problema de conectividade nó-a-nó, as credenciais podem estar dessincronizadas. Quando define credenciais para uma loja de dados no local que utiliza um gateway, guarda credenciais no nó de despacho/trabalhador. O nó do despachante sincroniza-se com outros nós operários. Este processo é conhecido como **sincronização de credenciais.** O canal de comunicação entre nós pode ser **encriptado** por um certificado SSL/TLS público. 

## <a name="set-up-a-multi-node-gateway"></a>Configurar um portal multi-nó
Esta secção pressupõe que tenha passado pelos seguintes dois artigos ou familiarizado com conceitos nestes artigos: 

- [Data Management Gateway](data-factory-data-management-gateway.md) - fornece uma visão geral detalhada do gateway.
- [Mover dados entre as lojas de dados no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) - contém uma passagem de passo a passo com instruções passo a passo para usar um gateway com um único nó.  

> [!NOTE]
> Antes de instalar uma porta de gestão de dados numa máquina Windows no local, consulte os pré-requisitos listados [no artigo principal](data-factory-data-management-gateway.md#prerequisites).

1. No [walkthrough](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), ao mesmo tempo que cria uma porta de entrada lógica, ativar a funcionalidade **de Alta Disponibilidade & Escalaability.** 

    ![Data Management Gateway - permitir alta disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na página **Configurar,** utilize uma **ligação de configuração expressa** ou **manual** para instalar um gateway no primeiro nó (uma máquina Windows no local).

    ![Data Management Gateway - configuração expressa ou manual](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se utilizar a opção de configuração expressa, a comunicação nó-a-nó é feita sem encriptação. O nome do nó é o mesmo que o nome da máquina. Utilize a configuração manual se a comunicação nó-nó precisar de ser encriptada ou se pretende especificar um nome de nó à sua escolha. Os nomes dos nóns não podem ser editados mais tarde.
3. Se escolher a **configuração expressa**
    1. Vê a seguinte mensagem depois de o gateway ser instalado com sucesso:

        ![Data Management Gateway - sucesso de configuração expressa](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Lançar Gestor de Configuração de Gestão de Dados para o gateway seguindo [estas instruções](data-factory-data-management-gateway.md#configuration-manager). Você vê o nome do portal, nome do nó, estado, etc.

        ![Screenshot que mostra onde você pode ver o nome do gateway, nome do nó e estado.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se escolher **a configuração manual:**
    1. Descarregue o pacote de instalação a partir do Microsoft Download Center, execute-o para instalar gateway na sua máquina.
    2. Utilize a chave de **autenticação** da página **Configure** para registar o gateway.
    
        ![Screenshot que mostra onde usar a chave de autenticação.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na página do **nó de novo gateway,** pode fornecer um **nome** personalizado ao nó de gateway. Por predefinição, o nome do nó é o mesmo que o nome da máquina.    

        ![Data Management Gateway - especificar nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na página seguinte, pode escolher se **permite a encriptação para comunicação nó-a-nó**. Clique **em Skip** para desativar a encriptação (predefinição).

        ![Data Management Gateway - ativar a encriptação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > A alteração do modo de encriptação só é suportada quando se tem um único nó de gateway no gateway lógico. Para alterar o modo de encriptação quando um gateway tiver múltiplos nós, faça os seguintes passos: elimine todos os nós exceto um nó, altere o modo de encriptação e, em seguida, adicione novamente os nós.
        > 
        > Consulte a secção [de requisitos de certificado TLS/SSL](#tlsssl-certificate-requirements) para obter uma lista de requisitos para a utilização de um certificado TLS/SSL. 
    5. Depois de o gateway ser instalado com sucesso, clique em Launch Configuration Manager:
    
        ![Configuração manual - gestor de configuração de lançamento](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. você vê data Management Gateway Configuration Manager no nó (no local máquina Windows), que mostra o estado de conectividade, **nome de porta de entrada** e nome do **nó**.  

        ![Data Management Gateway - instalação bem sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se estiver a aprovisionar o gateway num Azure VM, pode utilizar [este modelo de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Este script cria um gateway lógico, configura VMs com o software Data Management Gateway instalado, e regista-os com o gateway lógico. 
6. No portal Azure, lance a página **Gateway:** 
    1. Na página inicial da fábrica de dados no portal, clique nos **Serviços Linked**.
    
        ![Screenshot que destaca o azulejo dos serviços Linked.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. selecione o **portal** para ver a página **Gateway:**
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Vê a página **gateway:**   

        ![Gateway com vista única do nó](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Clique em **Adicionar Nó** na barra de ferramentas para adicionar um nó ao gateway lógico. Se estiver a planear utilizar a configuração expressa, faça este passo a partir da máquina no local que será adicionada como nó ao gateway. 

    ![Data Management Gateway - adicione menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Os passos são semelhantes à configuração do primeiro nó. O UI do Gestor de Configuração permite definir o nome do nó se escolher a opção de instalação manual: 

    ![Gestor de Configuração - instale o segundo gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Depois de o gateway ser instalado com sucesso no nó, a ferramenta 'Gestor de Configuração' apresenta o seguinte ecrã:  

    ![Configuration Manager - instale o segundo gateway com sucesso](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se abrir a página **Gateway** no portal, vê dois nóns de gateway agora: 

    ![Gateway com dois nóns no portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para eliminar um nó de gateway, clique em **Eliminar Nó** na barra de ferramentas, selecione o nó que pretende eliminar e, em seguida, clique em **Eliminar** a partir da barra de ferramentas. Esta ação elimina o nó selecionado do grupo. Note que esta ação não desinstala o software de gateway de gestão de dados a partir do nó (máquina Windows no local). Utilizar **Adicionar ou remover programas** no Painel de Controlo nas instalações para desinstalar o gateway. Quando desinstala o gateway a partir do nó, é automaticamente apagado no portal.   

## <a name="upgrade-an-existing-gateway"></a>Atualizar um portal existente
Pode atualizar uma porta de entrada existente para utilizar a funcionalidade de alta disponibilidade e escalabilidade. Esta funcionalidade funciona apenas com nós que têm a porta de entrada de gestão de dados da versão >= 2.12.xxxx. Pode ver a versão do gateway de gestão de dados instalada numa máquina no separador **Ajuda** do Gestor de Configuração gateway de gestão de dados. 

1. Atualize o gateway na máquina no local para a versão mais recente, através do download e execução de um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte a secção [de instalação](data-factory-data-management-gateway.md#installation) para mais detalhes.  
2. Navegue para o portal do Azure. Lance a **página data factory** para a sua fábrica de dados. Clique em azulejos de serviços ligados para lançar a **página de serviços ligados**. Selecione o portal para lançar a **página gateway**. Clique e ative **a funcionalidade de pré-visualização** como mostrado na seguinte imagem: 

    ![Data Management Gateway - ativar a funcionalidade de pré-visualização](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Uma vez ativada a funcionalidade de pré-visualização no portal, feche todas as páginas. Reabra a **página gateway** para ver a nova interface de utilizador de pré-visualização (UI).
 
    ![Data Management Gateway - permitir o sucesso do recurso de pré-visualização](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway - pré-visualização UI](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante a atualização, o nome do primeiro nó é o nome da máquina. 
3. Agora, adicione um nó. Na página **Gateway,** clique em **Adicionar Nó**.  

    ![Data Management Gateway - adicione menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga as instruções da secção anterior para configurar o nó. 

### <a name="installation-best-practices"></a>Melhores práticas de instalação

- Configure o plano de alimentação na máquina hospedeira para o gateway para que a máquina não se hiberna. Se a máquina hospedeira hibernar, o portal não responde aos pedidos de dados.
- Apoie o certificado associado ao portal.
- Certifique-se de que todos os nós são de configuração semelhante (recomendado) para o desempenho ideal. 
- Adicione pelo menos dois nós para garantir uma alta disponibilidade.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
Aqui estão os requisitos para o certificado TLS/SSL que é utilizado para assegurar comunicações entre nós de tempo de integração:

- O certificado deve ser um certificado X509 v3 de confiança pública. Recomendamos que utilize certificados emitidos por uma autoridade de certificação pública (terceira) (CA).
- Cada nó de execução de integração deve confiar neste certificado, bem como na máquina cliente que está executando a aplicação do gestor credencial. 
  > [!NOTE]
  > A aplicação de gestor credencial é utilizada enquanto se define de forma segura a credencial do Copy Wizard/Azure Portal. E isto pode ser disparado de qualquer máquina dentro da mesma rede que a loja de dados no local/ privada.
- Os certificados wild card são suportados. Se o seu nome FQDN for **node1.domain.contoso.com,** pode utilizar **_.domain.contoso.com_* como nome de assunto do certificado.
- Os certificados SAN não são recomendados, uma vez que apenas o último item dos Nomes Alternativos Sujeitos será utilizado e todos os outros serão ignorados devido à limitação atual. Por exemplo, você tem um certificado SAN cuja SAN são **node1.domain.contoso.com** e **node2.domain.contoso.com,** você só pode usar este certificado em máquina cuja FQDN é **node2.domain.contoso.com**.
- Suporta qualquer tamanho de chave suportado pelo Windows Server 2012 R2 para certificados TLS/SSL.
- O certificado que utiliza as teclas CNG não é suportado.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Quando não permitiria esta encriptação?
Ativar a encriptação pode adicionar determinado custo à sua infraestrutura (possuindo certificado público), pelo que pode saltar permitindo encriptação nos casos seguintes:
- Quando o tempo de integração está a decorrer numa rede fidedigna, ou numa rede com encriptação transparente como IP/SEC. Uma vez que esta comunicação de canal é limitada apenas dentro da sua rede fidedigna, pode não precisar de encriptação adicional.
- Quando o tempo de integração não está a decorrer num ambiente de produção. Isto pode ajudar a reduzir o custo do certificado TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorize um gateway de vários nós
### <a name="multi-node-gateway-monitoring"></a>Monitorização de gateways multi-nól
No portal Azure, pode visualizar imagens quase reais da utilização de recursos (CPU, memória, rede (in/out), etc.) em cada nó juntamente com os estados dos nós gateway. 

![Data Management Gateway - monitorização de múltiplos nóns](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Pode ativar **Definições Avançadas** na página **Gateway** para ver métricas avançadas como **Rede**(in/out), **Fun & Estado credencial**, que é útil na depurar problemas de gateway, e **Empregos Simultâneos** (Running/ Limit) que podem ser modificados/alterados em conformidade durante a afinação de desempenho. A tabela a seguir apresenta descrições de colunas na lista **de nóis gateway:**  

Propriedade de Monitorização | Descrição
:------------------ | :---------- 
Nome | Nome do portal lógico e nós associados ao portal.  
Estado | Estado do portal lógico e os nós de gateway. Exemplo: Online/Offline/Limited/etc. Para obter informações sobre estes estados, consulte a secção [de estado do Gateway.](#gateway-status) 
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão da maioria dos nós do grupo. Se existirem nós com versões diferentes na configuração lógica do gateway, apenas os nós com o mesmo número de versão que o gateway lógico funcionam corretamente. Outros encontram-se no modo limitado e precisam de ser atualizados manualmente (apenas no caso de falha de atualização automática). 
Memória disponível | Memória disponível num nó de gateway. Este valor é um instantâneo quase em tempo real. 
Utilização da CPU | Utilização do CPU de um nó de gateway. Este valor é um instantâneo quase em tempo real. 
Rede (In/out) | Utilização da rede de um nó de gateway. Este valor é um instantâneo quase em tempo real. 
Empregos Simultâneos (Execução/ Limite) | Número de empregos ou tarefas em cada nó. Este valor é um instantâneo quase em tempo real. O limite significa os empregos máximos simultâneos para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite para escalar a execução de emprego simultânea em cenários avançados, onde a CPU/memória/rede está subutilização, mas as atividades estão a cronometrar. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). Para mais informações, consulte a secção [de considerações de escala.](#scale-considerations) 
Função | Existem dois tipos de funções – Despacho e trabalhador. Todos os nós são trabalhadores, o que significa que todos podem ser usados para executar empregos. Há apenas um nó de despachante, que é usado para retirar tarefas/empregos dos serviços de nuvem e enviá-los para diferentes nós de trabalhadores (incluindo a si mesmo). 

![Data Management Gateway - monitorização avançada de múltiplos nóns](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Estado do gateway

A tabela a seguir fornece os possíveis estatutos de um nó de **gateway:** 

Estado  | Comentários/Cenários
:------- | :------------------
Online | Nó ligado ao serviço Data Factory.
Offline | O nó está desligado.
Modernização | O nó está a ser atualizado automaticamente.
Limitado | Devido a problemas de conectividade. Pode ser devido à emissão da porta HTTP 8050, problema de conectividade do autocarro de serviço ou problema de sincronização de credenciais. 
Inativa | O nó está numa configuração diferente da configuração de outros nós maioritários.<br/><br/> Um nó pode ser inativo quando não pode ligar-se a outros nós. 


A tabela seguinte fornece possíveis estatutos de um **portal lógico**. O estado do gateway depende do estado dos nós de gateway. 

Estado | Comentários
:----- | :-------
Registo de Necessidades | Nenhum nó está ainda registado neste portal lógico
Online | Os nóns de gateway estão online
Offline | Nenhum nó no estado online.
Limitado | Nem todos os nós desta porta estão em estado saudável. Este estado é um aviso de que algum nó pode estar em baixo! <br/><br/>Pode ser devido a problema de sincronização de credencial no nó de despachante/trabalhador. 

### <a name="pipeline-activities-monitoring"></a>Monitorização de gasodutos/atividades
O portal Azure proporciona uma experiência de monitorização do gasoduto com detalhes do nível do nó granular. Por exemplo, mostra quais as atividades em que nó. Esta informação pode ser útil para entender problemas de desempenho num determinado nó, por exemplo, devido ao estrangulamento da rede. 

![Data Management Gateway - monitorização de múltiplos noses para oleodutos](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway - detalhes do gasoduto](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerações de escala

### <a name="scale-out"></a>Aumentar horizontalmente
Quando a **memória disponível é baixa** e a utilização do **CPU é elevada,** adicionar um novo nó ajuda a escalar a carga através das máquinas. Se as atividades estiverem a falhar devido ao intervalo ou ao nó de gateway estar offline, ajuda se adicionar um nó ao gateway.
 
### <a name="scale-up"></a>Aumentar verticalmente
Quando a memória disponível e a CPU não são bem utilizadas, mas a capacidade de marcha lenta é 0, deve aumentar o número de empregos simultâneos que podem funcionar num nó. Você também pode querer aumentar quando as atividades estão a cronometrar porque o gateway está sobrecarregado. Como mostrado na imagem seguinte, pode aumentar a capacidade máxima para um nó. Sugerimos dourá-lo para começar.  

![Data Management Gateway - considerações de escala](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Questões conhecidas/alterações de rutura

- Atualmente, você pode ter até quatro nós de gateway físico para um único portal lógico. Se precisar de mais de quatro nós por razões de desempenho, envie um e-mail para [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) .
- Não é possível re-registar um nó de gateway com a chave de autenticação de outra porta lógica para mudar a partir do gateway lógico atual. Para voltar a registar-se, desinstale o gateway a partir do nó, reinstale o gateway e registe-o com a chave de autenticação para o outro gateway lógico. 
- Se for necessário um representante HTTP para todos os seus nós de gateway, desaprote o proxy em diahost.exe.config e diawp.exe.config e utilize o gestor do servidor para se certificar de que todos os nós têm os mesmos diahost.exe.config e diawip.exe.config. Consulte a secção [de configuração de configuração de configuração](data-factory-data-management-gateway.md#configure-proxy-server-settings) para obter mais informações. 
- Para alterar o modo de encriptação para a comunicação nó-a-nó no Gateway Configuration Manager, elimine todos os nós no portal, exceto um. Em seguida, adicione os nós de volta depois de alterar o modo de encriptação.
- Utilize um certificado oficial de TLS se optar por encriptar o canal de comunicação nó-a-nó. O certificado auto-assinado pode causar problemas de conectividade, uma vez que o mesmo certificado não pode ser confiado na lista de autoridades de certificação noutras máquinas. 
- Não é possível registar um nó de gateway para um gateway lógico quando a versão do nó é inferior à versão lógica do gateway. Elimine todos os nós do portal de gateway lógico para que possa registar um nó de versão inferior (downgrade) do mesmo. Se eliminar todos os nós de um gateway lógico, instale e registe manualmente novos nós para esse gateway lógico. A configuração expressa não é suportada neste caso.
- Não é possível utilizar a configuração expressa para instalar nós num gateway lógico existente, que ainda utiliza credenciais de nuvem. Pode verificar onde as credenciais são armazenadas a partir do Gestor de Configurações gateway no separador Definições.
- Não é possível utilizar a configuração expressa para instalar nós num gateway lógico existente, que tem encriptação nó-a-nó ativada. Uma vez que a definição do modo de encriptação envolve a adição manual de certificados, a instalação expressa não é mais uma opção. 
- Para uma cópia de ficheiro do ambiente no local, não deve mais utilizar \\ os locais ou os ficheiros C:\, uma vez que a unidade local ou a unidade local pode não estar acessível através de todos os nós. Em vez disso, utilize \\ o ServerName\files para especificar a localização dos ficheiros.


## <a name="rolling-back-from-the-preview"></a>Recuando da pré-estreia 
Para recuar da pré-visualização, elimine todos os nós, mas um nó. Não importa quais os nós que apaga, mas certifique-se de que tem pelo menos um nó no portal lógico. Pode eliminar um nó, desinstalando o gateway na máquina ou utilizando o portal Azure. No portal Azure, na página **Data Factory,** clique nos serviços Linked para lançar a página **de serviços Linked.** Selecione o portal para lançar a página **Gateway.** Na página Gateway, pode ver os nós associados ao gateway. A página permite-lhe apagar um nó do gateway.
 
Depois de eliminar, clique em **funcionalidades de pré-visualização** na mesma página do portal Azure e desative a funcionalidade de pré-visualização. Reiniciou a sua porta de entrada para um gateway DE NÓ GA (disponibilidade geral).


## <a name="next-steps"></a>Passos seguintes
Rever os seguintes artigos:
- [Data Management Gateway](data-factory-data-management-gateway.md) - fornece uma visão geral detalhada do gateway.
- [Mover dados entre as lojas de dados no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) - contém uma passagem de passo a passo com instruções passo a passo para usar um gateway com um único nó. 
