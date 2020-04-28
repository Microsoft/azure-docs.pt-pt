---
title: Alta disponibilidade com gateway de gestão de dados na Azure Data Factory
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
ms.openlocfilehash: 4ee89f4bba70bb5e81eef21247d556f65a2a1f16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80065200"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Gateway de Gestão de Dados - alta disponibilidade e escalabilidade (Pré-visualização)
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o tempo de [integração auto-hospedado em](../create-self-hosted-integration-runtime.md). 


Este artigo ajuda-o a configurar uma solução de alta disponibilidade e escalabilidade com gateway/integração de gestão de dados.    

> [!NOTE]
> Este artigo assume que já está familiarizado com os fundamentos do Tempo de Integração (Gateway de Gestão de Dados Anteriores). Caso contrário, consulte Data [Management Gateway](data-factory-data-management-gateway.md).
> 
> **Esta funcionalidade de pré-visualização é suportada oficialmente na versão Gateway de Gestão de Dados 2.12.xxxx.x e acima**. Certifique-se de que está a utilizar a versão 2.12.xxxx.x ou superior. Descarregue aqui a versão mais recente do [Portal](https://www.microsoft.com/download/details.aspx?id=39717)de Gestão de Dados .

## <a name="overview"></a>Descrição geral
Pode associar gateways de gestão de dados que estão instalados em várias máquinas no local com uma única porta de entrada lógica a partir do portal. Estas máquinas são chamadas **de nó.** Pode ter até **quatro nódoas associadas** a uma porta de entrada lógica. Os benefícios de ter vários nós (máquinas no local com gateway instalada) para um gateway lógico são:  

- Melhorar o desempenho do movimento de dados entre as lojas de dados no local e na nuvem.  
- Se um dos nós descer por alguma razão, outros nós ainda estão disponíveis para mover os dados. 
- Se um dos nós tiver de ser desligado para manutenção, outros nós ainda estão disponíveis para movimentar os dados.

Também pode configurar o número de **trabalhos simultâneos** de movimento de dados que podem funcionar num nó para aumentar a capacidade de movimentar dados entre as lojas de dados no local e as lojas de dados em nuvem. 

Utilizando o portal Azure, pode monitorizar o estado destes nós, o que o ajuda a decidir se deve adicionar ou remover um nó da porta de entrada lógica. 

## <a name="architecture"></a>Arquitetura 
O diagrama seguinte fornece a visão geral da arquitetura da escalabilidade e funcionalidade de disponibilidade do Gateway de Gestão de Dados: 

![Gateway de Gestão de Dados - Alta Disponibilidade e Escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Um **portal lógico** é a porta de entrada que se adiciona a uma fábrica de dados no portal Azure. Anteriormente, só se podia associar uma máquina windows no local com gateway de gestão de dados instalada com um portal lógico. Esta máquina de porta de entrada no local é chamada de nó. Agora, pode associar até **quatro nós físicos** com uma porta de entrada lógica. Um portal lógico com nómúltiplos é chamado de **porta de entrada de vários nódosos.**  

Todos estes nós estão **ativos.** Todos eles podem processar trabalhos de movimento de dados para mover dados entre as instalações e as lojas de dados em nuvem. Um dos nós atua como despachante e trabalhador. Outros nódosos dos grupos são os nódosos operários. Um **dispatcher** nó dedespacho retira tarefas/trabalhos de movimento de dados do serviço de nuvem e envia-os para nós de trabalhadores (incluindo a si mesmo). Um nó **de trabalhador** executa trabalhos de movimento de dados para mover dados entre as instalações e as lojas de dados na nuvem. Todos os nós são trabalhadores. Só um nó pode ser despachante e trabalhador.    

Normalmente, pode começar com um nó e **escalar** para adicionar mais nós, uma vez que os nóesos existentes estão sobrecarregados com a carga de movimento de dados. Também pode **aumentar** a capacidade de circulação de dados de um nó de gateway, aumentando o número de empregos simultâneos que são autorizados a funcionar no nó. Esta capacidade também está disponível com um gateway de um nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). 

Um portal com vários nós mantém as credenciais da loja de dados sincronizadas em todos os nós. Se houver um problema de conectividade nó-a-nó, as credenciais podem estar dessincronizadas. Quando define credenciais para uma loja de dados no local que utiliza um portal, guarda credenciais no nó de expedidor/trabalhador. O nó de despacho sincroniza com outros nódosos operários. Este processo é conhecido como **sincronização de credenciais.** O canal de comunicação entre nós pode ser **encriptado** por um certificado Público SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Criar um portal multi-nó
Esta secção pressupõe que você passou pelos dois artigos seguintes ou familiarizado com conceitos nestes artigos: 

- [Data Management Gateway](data-factory-data-management-gateway.md) - fornece uma visão detalhada da porta de entrada.
- [Mova os dados entre as instalações e as lojas de dados em nuvem](data-factory-move-data-between-onprem-and-cloud.md) - contém um walkthrough com instruções passo a passo para usar um gateway com um único nó.  

> [!NOTE]
> Antes de instalar um portal de gestão de dados numa máquina Windows no local, consulte os pré-requisitos listados [no artigo principal](data-factory-data-management-gateway.md#prerequisites).

1. No [walkthrough](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), ao mesmo tempo que cria um portal lógico, permite a **funcionalidade de alta disponibilidade & escalabilidade.** 

    ![Gateway de Gestão de Dados - permitir alta disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na página **Configure,** utilize o **express configuração** ou o link **de configuração manual** para instalar um gateway no primeiro nó (uma máquina windows no local).

    ![Gateway de Gestão de Dados - configuração expressa ou manual](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se utilizar a opção de configuração expressa, a comunicação nó-a-nó é feita sem encriptação. O nome do nó é o mesmo que o nome da máquina. Utilize a configuração manual se a comunicação nó-nó precisar de ser encriptada ou se pretender especificar um nome de nó à sua escolha. Nomes de nó não podem ser editados mais tarde.
3. Se escolher **configuração expressa**
    1. Veja a seguinte mensagem depois de o portal ser instalado com sucesso:

        ![Gateway de Gestão de Dados - sucesso de configuração expressa](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Lance o Gestor de Configuração de Gestão de Dados para o portal seguindo [estas instruções](data-factory-data-management-gateway.md#configuration-manager). Você vê o nome do portal, nome do nó, estado, etc.

        ![Gateway de Gestão de Dados - instalação bem sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se escolher a **configuração manual:**
    1. Descarregue o pacote de instalação do Microsoft Download Center e execute-o para instalar o gateway na sua máquina.
    2. Utilize a tecla de **autenticação** na página **Configure** para registar o gateway.
    
        ![Gateway de Gestão de Dados - instalação bem sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na página do **nó de gateway New,** você pode fornecer um **nome** personalizado para o nó de gateway. Por defeito, o nome do nó é o mesmo que o nome da máquina.    

        ![Gateway de Gestão de Dados - especifique o nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na página seguinte, pode escolher se permite a **encriptação para a comunicação nó-ao-nó**. Clique em **Skip** para desativar a encriptação (predefinido).

        ![Gateway de Gestão de Dados - ativar encriptação](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > A alteração do modo de encriptação só é suportada quando se tem um único nó de gateway na porta de entrada lógica. Para alterar o modo de encriptação quando um portal tem vários nós, faça os seguintes passos: apague todos os nós exceto um nó, altere o modo de encriptação e, em seguida, adicione os nós novamente.
        > 
        > Consulte a secção de requisitos de [certificado TLS/SSL](#tlsssl-certificate-requirements) para obter uma lista de requisitos para a utilização de um certificado TLS/SSL. 
    5. Depois de o gateway ser instalado com sucesso, clique no Gestor de Configuração de Lançamento:
    
        ![Configuração manual - gestor de configuração de lançamento](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. você vê Data Management Gateway Configuration Manager no nó (no local Máquina Windows), que mostra estado de conectividade, nome de **gateway**, e nome do **nó**.  

        ![Gateway de Gestão de Dados - instalação bem sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se estiver a fornecer a porta de entrada num VM Azure, pode utilizar este modelo de Gestor de [Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Este script cria uma porta de entrada lógica, configura VMs com software Gateway de Gestão de Dados instalado, e regista-os com a porta de entrada lógica. 
6. No portal Azure, lance a página **Gateway:** 
    1. Na página inicial da fábrica de dados no portal, clique em **Linked Services**.
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. selecione o **portal** para ver a página **Gateway:**
    
        ![Home page da fábrica de dados](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Vê a página **gateway:**   

        ![Gateway com vista única nó](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Clique em **Adicionar nó** na barra de ferramentas para adicionar um nó ao portal lógico. Se estiver a planear utilizar a configuração expressa, faça este passo da máquina no local que será adicionada como nó ao portal. 

    ![Gateway de Gestão de Dados - adicione menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Os passos são semelhantes à configuração do primeiro nó. O Gestor de Configuração UI permite definir o nome do nó se escolher a opção de instalação manual: 

    ![Gestor de Configuração - instale o segundo portal](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Depois de o gateway ser instalado com sucesso no nó, a ferramenta 'Gestor de Configuração' apresenta o seguinte ecrã:  

    ![Gestor de Configuração - instale o segundo gateway com sucesso](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se abrir a página **gateway** no portal, verá dois nós de gateway agora: 

    ![Gateway com dois nódosos no portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para eliminar um nó de gateway, clique em Eliminar o **Nó** na barra de ferramentas, selecione o nó que pretende eliminar e, em seguida, clique em **Apagar** a partir da barra de ferramentas. Esta ação elimina o nó selecionado do grupo. Note que esta ação não desinstala o software gateway de gestão de dados do nó (no local a máquina Windows). Utilize **adicionar ou remover programas** no Painel de Controlo no local para desinstalar o gateway. Quando desinstala o portal, é automaticamente apagado no portal.   

## <a name="upgrade-an-existing-gateway"></a>Atualizar um gateway existente
Pode atualizar uma porta de entrada existente para utilizar a funcionalidade de alta disponibilidade e escalabilidade. Esta funcionalidade funciona apenas com nós que têm o portal de gestão de dados da versão >= 2.12.xxxx. Pode ver a versão do portal de gestão de dados instalada numa máquina no separador **Ajuda** do Gestor de Configuração gateway de gestão de dados. 

1. Atualize o portal na máquina no local para a versão mais recente, seguindo-se o download e executando um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte a secção de [instalação](data-factory-data-management-gateway.md#installation) para obter mais detalhes.  
2. Navegue para o portal do Azure. Lance a **página Data Factory** para a sua fábrica de dados. Clique em azulejo de serviços Linked para lançar a página de **serviços ligados**. Selecione o portal para lançar a página de **gateway**. Clique e ative a funcionalidade de **pré-visualização** como mostrado na seguinte imagem: 

    ![Gateway de Gestão de Dados - ativar funcionalidade de pré-visualização](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Assim que a função de pré-visualização estiver ativada no portal, feche todas as páginas. Reabra a página de **gateway** para ver a nova interface de utilizador de pré-visualização (UI).
 
    ![Data Management Gateway - ativar o sucesso da funcionalidade de pré-visualização](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Gateway de Gestão de Dados - Pré-visualização UI](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante a atualização, o nome do primeiro nó é o nome da máquina. 
3. Agora, adicione um nó. Na página **Gateway,** clique em **Adicionar Nó**.  

    ![Gateway de Gestão de Dados - adicione menu de nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga as instruções da secção anterior para configurar o nó. 

### <a name="installation-best-practices"></a>Melhores práticas de instalação

- Configure o plano de alimentação na máquina hospedeira para o portal para que a máquina não hiberna. Se a máquina hospedeira hibernar, o portal não responde aos pedidos de dados.
- Volte o certificado associado ao portal.
- Certifique-se de que todos os nós são de configuração semelhante (recomendado) para o desempenho ideal. 
- Adicione pelo menos dois nódosos para garantir uma alta disponibilidade.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
Aqui estão os requisitos para o certificado TLS/SSL que é utilizado para garantir comunicações entre os nós de tempo de execução de integração:

- O certificado deve ser um certificado X509 v3 de confiança pública. Recomendamos que utilize certificados emitidos por uma autoridade de certificação pública (terceiros) (CA).
- Cada nó de tempo de funcionamento de integração deve confiar neste certificado, bem como na máquina cliente que está executando a aplicação de gestor de credenciais. 
  > [!NOTE]
  > A aplicação do gestor credencial é utilizada enquanto define seguramente a credencial do Copy Wizard/Portal Azure. E isto pode ser disparado a partir de qualquer máquina dentro da mesma rede que a loja de dados privada no local.
- Os certificados de cartão selvagem são suportados. Se o seu nome FQDN for **node1.domain.contoso.com,** pode utilizar ***.domain.contoso.com** como nome de assunto do certificado.
- Os certificados SAN não são recomendados, uma vez que apenas o último item dos Nomes Alternativos sujeitos será usado e todos os outros serão ignorados devido à limitação atual. Por exemplo, você tem um certificado SAN cujo SAN é **node1.domain.contoso.com** e **node2.domain.contoso.com,** você só pode usar este certificado na máquina cujo FQDN é **node2.domain.contoso.com**.
- Suporta qualquer tamanho de chave suportado pelo Windows Server 2012 R2 para certificados TLS/SSL.
- O certificado que utiliza as teclas CNG não é suportado.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>FAQ: Quando é que eu não permitiria esta encriptação?
Permitir a encriptação pode adicionar determinado custo à sua infraestrutura (possuir certificado público) pelo que pode saltar para permitir encriptação nos casos abaixo:
- Quando o tempo de funcionamento da integração está a decorrer numa rede de confiança, ou numa rede com encriptação transparente como ip/SEC. Uma vez que esta comunicação do canal é limitada apenas dentro da sua rede fidedigna, pode não precisar de encriptação adicional.
- Quando o tempo de funcionamento da integração não está a funcionar num ambiente de produção. Isto pode ajudar na redução do custo do certificado TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorize um portal de vários nós
### <a name="multi-node-gateway-monitoring"></a>Monitorização de gateway sumo de nó
No portal Azure, pode ver instantâneos quase em tempo real de utilização de recursos (CPU, memória, rede (dentro/fora), etc.) em cada nó juntamente com os estados dos nós de gateway. 

![Gateway de Gestão de Dados - monitorização múltipla do nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Pode ativar **configurações avançadas** na página **Gateway** para ver métricas avançadas como **Rede**(in/out), **Role & Credencial Status**, que é útil para depurar problemas de gateway, e **Empregos Simultâneos** (Running/Limit) que podem ser modificados/alterados em conformidade durante a sintonização do desempenho. A tabela seguinte apresenta descrições de colunas na lista gateway **nódosos:**  

Propriedade de Monitorização | Descrição
:------------------ | :---------- 
Nome | Nome da porta de entrada lógica e nódoas associadas à porta de entrada.  
Estado | Estado da porta de entrada lógica e dos nódosos de entrada. Exemplo: Online/Offline/Limited/etc. Para obter informações sobre estes estados, consulte a secção [de estado gateway.](#gateway-status) 
Versão | Mostra a versão do portal lógico e cada nó de gateway. A versão do portal lógico é determinada com base na versão da maioria dos nódosos do grupo. Se houver nós com versões diferentes na configuração lógica do portal, apenas os nós com o mesmo número de versão que a função lógica do gateway funcionam corretamente. Outros estão no modo limitado e precisam de ser atualizados manualmente (apenas no caso de a atualização automática falhar). 
Memória disponível | Memória disponível em um nó de entrada. Este valor é um instantâneo quase em tempo real. 
Utilização da CPU | Utilização da CPU de um nó de gateway. Este valor é um instantâneo quase em tempo real. 
Networking (In/out) | Utilização da rede de um nó de entrada. Este valor é um instantâneo quase em tempo real. 
Empregos simultâneos (execução/ limite) | Número de postos de trabalho ou tarefas em cada nó. Este valor é um instantâneo quase em tempo real. Limite significa o máximo de empregos simultâneos para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite para aumentar a execução de emprego simultânea em cenários avançados, onde a CPU/memória/rede está subutilizada, mas as atividades estão a cronometrar. Esta capacidade também está disponível com um gateway de um nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada). Para mais informações, consulte a secção [de considerações](#scale-considerations) de escala. 
Função | Existem dois tipos de funções – Despachante e trabalhador. Todos os nós são trabalhadores, o que significa que todos podem ser usados para executar empregos. Existe apenas um nó deexpedidor, que é usado para retirar tarefas/empregos dos serviços de nuvem e despachá-los para diferentes nós de trabalhador (incluindo a si mesmo). 

![Gateway de Gestão de Dados - monitorização avançada de nó múltiplo](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Estado do gateway

O quadro seguinte fornece possíveis estatutos de um nó de **gateway:** 

Estado  | Comentários/Cenários
:------- | :------------------
Online | Nó ligado ao serviço data Factory.
Offline | O nó está offline.
Upgrade | O nó está a ser atualizado automaticamente.
Limitado | Devido a um problema de conectividade. Pode ser devido a http porta 8050, problema de conectividade de ônibus de serviço, ou problema de sincronização credencial. 
Inativa | O nó está numa configuração diferente da configuração de outros nódosos maioritários.<br/><br/> Um nó pode estar inativo quando não consegue ligar-se a outros nódosos. 


A tabela que se segue fornece possíveis estatutos de uma **porta de entrada lógica**. O estado do portal depende dos estados dos nódeos de gateway. 

Estado | Comentários
:----- | :-------
Necessidades De Inscrição | Nenhum nó ainda está registado nesta porta de entrada lógica.
Online | Os nódosos gateway estão online
Offline | Não nó em estado on-line.
Limitado | Nem todos os nós nesta porta estão em estado saudável. Este estado é um aviso de que algum nó pode estar em baixo! <br/><br/>Pode ser devido a uma questão de sincronização credencial no nó de expedidor/trabalhador. 

### <a name="pipeline-activities-monitoring"></a>Monitorização de gasodutos/atividades
O portal Azure proporciona uma experiência de monitorização do gasoduto com detalhes do nível do nó granular. Por exemplo, mostra quais as atividades em que o nó. Esta informação pode ser útil para compreender problemas de desempenho num determinado nó, por exemplo, devido ao estrangulamento da rede. 

![Gateway de Gestão de Dados - monitorização múltipla do nó para oleodutos](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Gateway de Gestão de Dados - detalhes do pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerações de escala

### <a name="scale-out"></a>Aumentar horizontalmente
Quando a **memória disponível é baixa** e o uso do **CPU é elevado,** adicionar um novo nó ajuda a escalar a carga através das máquinas. Se as atividades estiverem a falhar devido ao tempo de paragem ou ao nó de gateway estar offline, ajuda se adicionar um nó ao portal.
 
### <a name="scale-up"></a>Aumentar verticalmente
Quando a memória disponível e a CPU não são bem utilizadas, mas a capacidade inativa é 0, deve aumentar o número de empregos simultâneos que podem funcionar num nó. Você também pode querer escalar quando as atividades estão cronometradas porque o gateway está sobrecarregado. Como mostra a imagem seguinte, pode aumentar a capacidade máxima para um nó. Sugerimos que o dominá-lo para começar.  

![Gateway de Gestão de Dados - considerações de escala](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Questões conhecidas/alterações de rutura

- Atualmente, você pode ter até quatro nós de gateway físico para uma única porta de entrada lógica. Se precisar de mais de quatro nós por razões de desempenho, envie um e-mail para [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com).
- Não é possível voltar a registar um nó de gateway com a chave de autenticação de outra porta de entrada lógica para mudar do portal lógico atual. Para voltar a registar, desinstale o portal a partir do nó, reinstale o gateway e registe-o com a chave de autenticação para o outro portal lógico. 
- Se for necessário procuração HTTP para todos os seus nós de gateway, coloque o proxy em diahost.exe.config e diawp.exe.config, e use o gestor do servidor para se certificar de que todos os nós têm o mesmo diahost.exe.config e diawip.exe.config. Consulte a secção de definições de [procuração](data-factory-data-management-gateway.md#configure-proxy-server-settings) para obter mais detalhes. 
- Para alterar o modo de encriptação para a comunicação nó-a-nó no Gateway Configuration Manager, elimine todos os nós do portal exceto um. Em seguida, adicione os nós de volta depois de alterar o modo de encriptação.
- Utilize um certificado TLS oficial se optar por encriptar o canal de comunicação nó-a-nó. O certificado auto-assinado pode causar problemas de conectividade, uma vez que o mesmo certificado não pode ser confiado na lista de autoridade sancionada noutras máquinas. 
- Não é possível registar um nó de entrada para uma porta de entrada lógica quando a versão do nó é inferior à versão lógica do gateway. Elimine todos os nós da porta de entrada lógica do portal para que possa registar um nó de versão inferior (downgrade) dele. Se eliminar todos os nós de um portal lógico, instale manualmente e registe novos nós nesse portal lógico. A configuração expressa não é apoiada neste caso.
- Não é possível utilizar a configuração expressa para instalar nós numa porta de entrada lógica existente, que ainda utiliza credenciais de nuvem. Pode verificar onde as credenciais são armazenadas no Gateway Configuration Manager no separador Definições.
- Não é possível utilizar a configuração expressa para instalar nós numa porta de entrada lógica existente, que tem encriptação nó-a-nó ativada. Como a definição do modo de encriptação envolve a adição manual de certificados, a instalação expressa já não é uma opção. 
- Para uma cópia de ficheiro do ambiente no \\local, não deve mais utilizar o local de acolhimento ou os ficheiros C:\, uma vez que o local ou a unidade local podem não estar acessíveis através de todos os nós. Em vez \\disso, utilize ficheiros ServerName\para especificar a localização dos ficheiros.


## <a name="rolling-back-from-the-preview"></a>Voltando da pré-visualização 
Para recuar da pré-visualização, apague todos os nós, menos um nó. Não importa quais os nós que apaga, mas certifique-se de que tem pelo menos um nó na porta de entrada lógica. Pode eliminar um nó desinstalando o portal Azure ou utilizando o portal Azure. No portal Azure, na página **Data Factory,** clique nos serviços linked para lançar a página de **serviços Linked.** Selecione o portal para lançar a página **Gateway.** Na página Gateway, pode ver os nós associados ao portal. A página permite-lhe apagar um nó a partir do portal.
 
Depois de apagar, clique nas funcionalidades de **pré-visualização** na mesma página do portal Azure e desative a função de pré-visualização. Redefiniu a sua porta de entrada para um portal GA (disponibilidade geral).


## <a name="next-steps"></a>Passos seguintes
Reveja os seguintes artigos:
- [Data Management Gateway](data-factory-data-management-gateway.md) - fornece uma visão detalhada da porta de entrada.
- [Mova os dados entre as instalações e as lojas de dados em nuvem](data-factory-move-data-between-onprem-and-cloud.md) - contém um walkthrough com instruções passo a passo para usar um gateway com um único nó. 
