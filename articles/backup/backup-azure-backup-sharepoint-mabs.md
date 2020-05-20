---
title: De volta a uma fazenda SharePoint para Azure com MABS
description: Utilize o Servidor de Backup Azure para fazer backup e restaurar os seus dados do SharePoint. Este artigo fornece as informações para configurar a sua quinta SharePoint para que os dados desejados possam ser armazenados em Azure. Pode restaurar os dados do SharePoint protegidos a partir do disco ou do Azure.
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 7e429eeb5319a12c3483510072fd82c69c8d8ab3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657284"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>De volta a uma fazenda SharePoint para Azure com MABS

Você faz backup de uma fazenda SharePoint para o Microsoft Azure usando o Microsoft Azure Backup Server (MABS) da mesma forma que você faz backup de outras fontes de dados. O Azure Backup oferece flexibilidade na programação de backup para criar pontos de backup diários, semanais, mensais ou anuais e oferece opções de política de retenção para vários pontos de backup. A MABS fornece a capacidade de armazenar cópias de discos locais para objetivos de tempo de recuperação rápida (RTO) e de armazenar cópias para o Azure para retenção económica e a longo prazo.

Apoiar o SharePoint para Azure com MABS é um processo semelhante para apoiar o SharePoint para dPM (Data Protection Manager) localmente. No presente artigo serão observadas considerações particulares sobre o Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>O SharePoint suportaversões suportadas e cenários de proteção conexos

Para obter uma lista de versões SharePoint suportadas e as versões MABS necessárias para as apoiar, consulte a matriz de [proteção MABS](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix)

## <a name="before-you-start"></a>Antes de começar

Há algumas coisas que precisa confirmar antes de voltar a uma fazenda SharePoint para Azure.

### <a name="whats-not-supported"></a>O que não é suportado

* O MABS que protege uma quinta do SharePoint não protege índices de pesquisa ou bases de dados de serviços de aplicação. Terá de configurar a proteção destas bases de dados separadamente.

* O MABS não fornece cópias de segurança das bases de dados do SharePoint SQL Server que estão alojadas em partilhas de servidores de ficheiros scale-out (SOFS).

### <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que cumpriu todos os [pré-requisitos para a utilização](backup-azure-dpm-introduction.md#prerequisites-and-limitations) do Microsoft Azure Backup para proteger as cargas de trabalho. Algumas tarefas para pré-requisitos incluem: criar um cofre de reserva, baixar credenciais de cofre, instalar o Agente de Backup Azure e registar o Servidor de Backup Azure com o cofre.

Pré-requisitos e limitações adicionais:

* Por predefinição quando proteger o SharePoint, todas as bases de dados de conteúdos (e as bases de dados SharePoint_Config e SharePoint_AdminContent*) serão protegidas. Se quiser adicionar personalizações, tais como índices de pesquisa, modelos ou bases de dados de serviço de aplicação, ou o serviço de perfis de utilizador, terá de os configurar separadamente para proteção. Certifique-se de que ativa a proteção para todas as pastas que incluem estes tipos de funcionalidades ou ficheiros de personalização.

* Não pode proteger as bases de dados do SharePoint como origens de dados do SQL Server. Pode recuperar bases de dados individuais a partir da cópia de segurança de um farm.

* Lembre-se que o MABS funciona como **Sistema Local**, e para fazer o back-up das bases de dados do SQL Server precisa de privilégios de sysadmin nessa conta para o servidor SQL. No Servidor SQL pretende fazer o back-up, configurar o NT AUTHORITY\SYSTEM para **sinsadmina**.

* Por cada 10 milhões de itens na exploração, deve haver pelo menos 2 GB de espaço no volume onde está localizada a pasta MABS. Este espaço é necessário para a geração do catálogo. Para permitir utilizar o MABS para realizar uma recuperação específica de itens (coleções de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração de catálogos cria uma lista dos URLs contidos em cada base de dados de conteúdos. Pode visualizar a lista de URLs no painel de itens recuperável na área de tarefa de recuperação da Consola de Administrador MABS.

* Na quinta SharePoint, se tiver bases de dados do SQL Server configuradas com pseudónimos do SQL Server, instale os componentes do cliente do Servidor SQL no servidor Web frontal que o MABS irá proteger.

* A proteção dos itens de armazenamento da aplicação não é suportada no SharePoint 2013.

* O MABS não suporta proteger o FILESTREAM remoto. O FILESTREAM deve fazer parte da base de dados.

## <a name="configure-backup"></a>Configurar a cópia de segurança

Para fazer o apoio à exploração SharePoint, configure a proteção para o SharePoint utilizando o ConfigureSharePoint.exe e, em seguida, crie um grupo de proteção em MABS.

1. **Execute o ConfigureSharePoint.exe** – esta ferramenta configura o Serviço de Escritor VSS do SharePoint \(WSS\) e fornece o agente de proteção com as credenciais para o farm do SharePoint. Depois de ter implementado o agente de proteção, o ficheiro ConfigureSharePoint.exe pode ser encontrado na `<MABS Installation Path\>\bin` pasta no servidor Web \- frontal.  Se tiver vários servidores WFE, só precisa de o instalar num deles. Execute o comando da seguinte forma:

    * No servidor WFE, a uma solicitação de comando navegar `\<MABS installation location\>\\bin\\` para e `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` executar, onde:

        * **EnableSharePointProtection** permite a proteção da quinta SharePoint, permite ao escritor VSS, e regista a identidade da aplicação DCOM WssCmdletsWrapper para funcionar como um utilizador cujas credenciais são introduzidas com esta opção. Esta conta deve ser um administrador de farm, bem como um administrador local no Servidor Web front\-end.

        * **EnableSPSearchProtection** ativa a proteção de WSS 3.0 SP Search utilizando a chave de registo SharePointSearchEnumerationEnabled em HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ no Servidor Web front\-end e regista a identidade da aplicação WssCmdletsWrapper do DCOM para ser executada como um utilizador cujas credenciais são introduzidas com esta opção. Esta conta deve ser um administrador de farm, bem como um administrador local no Servidor Web front\-end.

        * **ResolveAllSQLAliases** apresenta todos os aliases comunicados pelo escritor VSS do SharePoint e resolve-os para o SQL Server correspondente. Também apresenta os nomes de instância dos aliases resolvidos. Se os servidores forem espelhados, apresentará também o servidor espelhado. Relata todos os pseudónimos que não estão a ser resolvidos a um Servidor SQL.

        * **SetTempPath** define as variáveis de ambiente TEMP e TMP para o caminho especificado. A recuperação do nível do item falha se uma grande recolha de site, site, lista ou item estiver sendo recuperado e não há espaço suficiente na pasta temporária de administração da fazenda. Esta opção permite alterar o caminho de pasta dos ficheiros temporários para um volume que tenha espaço suficiente para armazenar a coleção de sites ou o site que estão a ser recuperados.

    * Introduza as credenciais de administrador do farm. Esta conta deve ser um membro do grupo de Administrador local no servidor WFE. Se o administrador da fazenda não for um administrador local, conceda as seguintes permissões no servidor WFE:

        * Conceda ao grupo WSS \_ Admin \_ WPG o controlo total da pasta MABS \( %Program Files% \\ Data Protection Manager \\ DPM \) .
            -A

        * Conceda ao \_ grupo WSS Admin WPG ler o acesso à chave de \_ registo MABS \( HKEY LOCAL MACHINE SOFTWARE \_ Microsoft Microsoft Data Protection Manager \_ \\ \\ \\ \) .

        Depois de executar ConfigureSharePoint.exe, terá de reexecutá-lo se houver uma alteração nas credenciais de administrador da fazenda SharePoint.

1. Para criar um grupo **Protection**de proteção, clique em Ações de  >  **Actions**  >  **Proteção Criar Grupo** de Proteção para abrir o assistente do Grupo de **Proteção Nova** na consola MABS.

1. Em **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores**.

1. Em **Select Group Members**, expanda o servidor que detém a função WFE. Se houver mais de um servidor WFE, selecione aquele em que instalou o ConfigureSharePoint.exe.

    Quando expande as consultas mABS do servidor SharePoint VSS para ver que dados o MABS pode proteger.  Se a base de dados SharePoint for remota, o MABS liga-se à sua. Se as fontes de dados do SharePoint não aparecerem, verifique se o escritor VSS está a funcionar no servidor SharePoint e em qualquer servidor SQL remoto, e certifique-se de que o agente MABS está instalado tanto no servidor SharePoint como no servidor SQL remoto. Além disso, certifique-se de que as bases de dados do SharePoint não estão a ser protegidas noutros locais como bases de dados do Servidor SQL.

1. No **método de proteção**de dados Select, especifique como pretende lidar com a cópia de segurança a curto e a longo \- prazo. As cópias de segurança de curto\- prazo são sempre efetuadas para o disco em primeiro lugar, com a opção de criar a cópia de segurança do disco para a nuvem do Azure com a cópia de segurança do Azure \(para curto ou longo\- prazo\).

1. Em Selecionar objetivos de **curto \- prazo,** especifique como pretende fazer o armazenamento de curto \- prazo no disco.   Na **gama Retenção** especifica quanto tempo pretende manter os dados no disco. Na **frequência de sincronização,** especifice com que frequência pretende executar uma cópia de segurança incremental para o disco. Se não quiser definir um intervalo de backup, pode verificar antes de um ponto de recuperação para que o MABS execute um backup completo expresso antes de cada ponto de recuperação estar agendado.

1. Na página de atribuição de discos de revisão, reveja o espaço de disco de armazenamento atribuído ao grupo de proteção.

    **O tamanho total de Dados** é o tamanho dos dados que pretende fazer cópia sinuoso, e o espaço do Disco a fornecer no **MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume em **Detalhes de atribuição do disco**. Para cargas de trabalho, selecione o armazenamento preferido no menu de lista pendente. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Gratuito** no painel **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento MABS sugere que adicione ao volume, para continuar com backups suavemente no futuro.

1. Em Escolha o método de **criação**de réplicas, selecione como pretende lidar com a replicação inicial de dados completos.  Se selecionar replicar-se sobre a rede, recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou condições de rede não ideais, considere replicar os dados offline com um suporte de dados amovível.

1. Em **Selecionar opções de verificação de consistência**, selecione como pretende automatizar as verificações de consistência. Pode ativar a execução de uma verificação apenas quando os dados de réplica ficarem inconsistentes ou de acordo com uma agenda. Se não pretender configurar a verificação automática de consistência, pode fazer uma verificação manual a qualquer momento clicando no grupo de proteção na área de **proteção** da consola MABS e selecionando a **Verificação**de Consistência de Executar .

1. Se tiver optado por criar a cópia de segurança para a nuvem com o Azure Backup, na página **Especificar proteção de dados online**, certifique-se de que as cargas de trabalho das quais pretende criar uma cópia de segurança para o Azure estão selecionadas.

1. Em **especificar o calendário de cópias de segurança on-line,** especifique com que frequência devem ocorrer cópias de segurança incrementais para o Azure. Pode agendar as cópias de segurança para serem executadas todos os dias/semanas/meses/anos, bem como a data e hora de execução. As cópias de segurança podem ocorrer, no máximo, duas vezes por dia. Cada vez que uma cópia de segurança é executado, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de reserva armazenados no disco MABS.

1. Em Especificar a política de **retenção on-line,** pode especificar como os pontos de recuperação criados a partir das cópias de segurança diárias/semanais/mensais/anuais são mantidos no Azure.

1. Em **Choose replicação on-line,** especifique como ocorrerá a replicação completa inicial dos dados. Pode replicar através da rede ou criar uma cópia de segurança offline (propagação offline). A cópia de segurança offline utiliza a funcionalidade de Importação do Azure. [Leia mais.](https://azure.microsoft.com/documentation/articles/backup-azure-backup-import-export/)

1. Na página **Resumo,** reveja as suas definições. Depois de clicar no **Grupo Criar**, a replicação inicial dos dados ocorre. Quando terminar, o estado do grupo de proteção mostrará como **OK** na página **'Status'.** A criação da cópia de segurança está em conformidade com as definições do grupo de proteção.

## <a name="monitoring"></a>Monitorização

Após a criação do grupo de proteção, a replicação inicial ocorre e o MABS começa a fazer backup e sincronização dos dados do SharePoint. O MABS monitoriza a sincronização inicial e as cópias de segurança subsequentes.  Pode monitorizar os dados do SharePoint de algumas formas:

* Utilizando a monitorização padrão do MABS, pode configurar notificações para monitorização proactiva, publicando alertas e configurando notificações. Pode enviar notificações por e-mail para alertas críticos, de aviso ou informativos e para o estado de recuperações instanciadas.

* Se utilizar o Gestor de Operações, pode publicar centralmente alertas.

### <a name="set-up-monitoring-notifications"></a>Configurar as notificações de monitorização

1. Na consola de administrador mABS, clique em **Opções de**Ação de Monitorização  >  **Action**  >  **Options**.

2. Clique em **Servidor SMTP**, introduza o nome do servidor, a porta e o endereço de e-mail a partir dos quais as notificações serão enviadas. O endereço tem de ser válido.

3. No **servidor SMTP autenticado,** escreva um nome de utilizador e uma palavra-passe. O nome do utilizador e a palavra-passe devem ser o nome da conta de domínio da pessoa cujo endereço "From" é descrito no passo anterior. Caso contrário, a entrega de notificação falha.

4. Para testar as definições do servidor SMTP, clique em **Enviar E-mail,** digite o endereço de e-mail onde pretende que o MABS envie a mensagem de teste e, em seguida, clique em **OK**. Clique **Options**em  >  **Opções Notificações** e selecione os tipos de alertas sobre quais os destinatários que desejam ser notificados. Nos **Destinatários** digite o endereço de e-mail para cada destinatário a quem pretende que o MABS envie cópias das notificações.

### <a name="publish-operations-manager-alerts"></a>Publicar alertas do Operations Manager

1. Na consola de administrador mABS, clique em **Monitorizar**opções de  >  **ação**  >  **Options**  >  **Publicar**  >  **Alertas Ativos**

2. Depois de ativar a Publicação de **Alertas,** todos os alertas MABS existentes que podem exigir uma ação do utilizador são publicados no registo do evento **MABS Alerts.** O agente DoGestor de Operações que está instalado no servidor MABS publica então estes alertas para o Gestor de Operações e continua a atualizar a consola à medida que novos alertas são gerados.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item SharePoint a partir do disco utilizando MABS

No exemplo seguinte, o *item Recovery SharePoint* foi acidentalmente eliminado e precisa de ser recuperado.
![Proteção do Ponto de Partilha MABS4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra a consola de **administrador mABS**. Todas as explorações sharePoint protegidas por MABS são mostradas no separador **Proteção.**

    ![Proteção do Ponto de Partilha MABS3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o artigo, selecione o separador **Recovery.**

    ![Proteção do Ponto de Partilha MABS5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Pode pesquisar sharePoint para *recuperar o item sharePoint* utilizando uma pesquisa baseada em wildcard dentro de um intervalo de ponto de recuperação.

    ![Proteção do Ponto de Partilha MABS6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriado dos resultados da pesquisa, clique no item para a direita e, em seguida, **selecione Recuperar**.
5. Também pode navegar por vários pontos de recuperação e selecionar uma base de dados ou item para recuperar. Selecione data > tempo de **recuperação**e, em seguida, selecione a base de dados correta **> fazenda SharePoint > ponto**de recuperação > ponto de recuperação .

    ![Proteção do Ponto de Partilha MABS7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Clique no item para a direita e, em seguida, selecione **Recuperar** para abrir o **Assistente de Recuperação**. Clique em **Seguinte**.

    ![Seleção de Recuperação de Avaliação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que pretende realizar e, em seguida, clique em **Seguinte**.

    ![Tipo de recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **Recuperar para original** no exemplo recupera o item para o site original do SharePoint.
   >
   >
8. Selecione o Processo de **Recuperação** que pretende utilizar.

   * Selecione **Recuperar sem utilizar uma quinta de recuperação** se a quinta SharePoint não tiver mudado e for o mesmo que o ponto de recuperação que está a ser restaurado.
   * Selecione Recuperar utilizando uma quinta de **recuperação** se a exploração SharePoint tiver mudado desde que o ponto de recuperação foi criado.

     ![Processo de Recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça uma localização de instância de sql server de encenação para recuperar temporariamente a base de dados e fornecer uma partilha de ficheiros de encenação no MABS e no servidor que está a executar o SharePoint para recuperar o item.

    ![Localização de encenação1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    O MABS anexa a base de dados de conteúdo que está a hospedar o item SharePoint à instância temporária do Servidor SQL. A partir da base de dados de conteúdos, recupera o item e coloca-o na localização do ficheiro de encenação no MABS. O item recuperado que está no local de preparação precisa agora de ser exportado para o local de paragem na fazenda SharePoint.

    ![Local de encenação2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **Selecione Especificar opções**de recuperação e aplicar as definições de segurança na exploração do SharePoint ou aplicar as definições de segurança do ponto de recuperação. Clique em **Seguinte**.

    ![Opções de Recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Pode optar por acelerar o uso da largura de banda da rede. Isto minimiza o impacto no servidor de produção durante o horário de produção.
    >
    >
11. Reveja as informações sumárias e, em seguida, clique em **Recuperar** para iniciar a recuperação do ficheiro.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecione agora o separador **monitorizador** na Consola de **Administrador MABS** para ver o **estado** da recuperação.

    ![Estado de Recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > O ficheiro está agora restaurado. Pode atualizar o site sharePoint para verificar o ficheiro restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Restaurar uma base de dados SharePoint a partir do Azure utilizando mABS

1. Para recuperar uma base de dados de conteúdo sharePoint, navegue por vários pontos de recuperação (como mostrado anteriormente), e selecione o ponto de recuperação que pretende restaurar.

    ![Proteção do Ponto de Partilha MABS8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Uma vez que a exploração SharePoint está protegida para retenção a longo prazo no Azure, não há informações de catálogo (metadados) disponíveis no servidor MABS. Como resultado, sempre que uma base de dados de conteúdo sharePoint ponto-a-tempo precisa de ser recuperada, precisa de catalogar novamente a quinta SharePoint.
   >
   >
3. Clique em **Re-catalogar**.

    ![Proteção do Ponto de Partilha MABS10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de estado do **Cloud Recatalog** abre-se.

    ![Proteção do Ponto de Partilha MABS11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação estar terminada, o estado muda para *O Sucesso*. Clique em **Fechar**.

    ![Proteção do Ponto de Partilha MABS12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto SharePoint mostrado no separador **De recuperação** MABS para obter a estrutura da base de dados de conteúdos. Clique no item para a direita e, em seguida, clique em **Recuperar**.

    ![Proteção do Ponto de Partilha MABS13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste ponto, siga os passos de recuperação no início deste artigo para recuperar uma base de dados de conteúdo sharePoint a partir do disco.

## <a name="switching-the-front-end-web-server"></a>Mudar o servidor web front-end

Se tiver mais de um servidor web frontal e pretender mudar o servidor que o MABS utiliza para proteger a exploração, siga as instruções:

O procedimento seguinte utiliza o exemplo de uma quinta de servidores com dois servidores Web front-end, *Server1* e *Server2*. O MABS usa o *Server1* para proteger a quinta. Altere o servidor Web frontal que o MABS utiliza para *o Server2* para que possa remover o *Server1* da quinta.

> [!NOTE]
> Se o servidor Web frontal que o MABS utiliza para proteger a exploração não estiver disponível, utilize o seguinte procedimento para alterar o servidor Web frontal, a partir do passo 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Para alterar o servidor Web frontal que o MABS usa para proteger a exploração

1. Pare o serviço de escritor SharePoint VSS no *Server1* executando o seguinte comando a uma solicitação de comando:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. No *Server1,* abra o Editor de Registo e navegue para a seguinte tecla:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Verifique todos os valores listados na subchave VssAccessControl. Se alguma entrada tiver um valor de 0 e outro escritor VSS estiver a funcionar sob as credenciais de conta associadas, altere os dados de valor para 1.

1. Instale um agente de proteção no *Server2*.

   > [!WARNING]
   > Só pode mudar os servidores frontais da Web se ambos os servidores estiverem no mesmo domínio.

1. No *Server2*, a uma solicitação de comando, altere o diretório `_MABS installation location_\bin\` para e **execute o ConfigureSharepoint**. Para mais informações sobre o ConfigureSharePoint, consulte a [cópia de segurança configure](#configure-backup).

1. Selecione o grupo de proteção a que a quinta do servidor pertence e, em seguida, clique em **Modificar o grupo de proteção**.

1. No ModificadoR do Assistente do Grupo, na página **Select Group Members,** expanda o *Server2* e selecione a quinta do servidor e, em seguida, complete o assistente.

   Uma verificação de consistência vai começar.

1. Se executou o passo 6, pode agora remover o volume do grupo de proteção.

## <a name="next-steps"></a>Passos seguintes

Consulte o artigo do [servidor Back up Exchange.](backup-azure-exchange-mabs.md)
Consulte o artigo [do SQL Server](backup-azure-sql-mabs.md) de back up.
