---
title: Apoiar uma quinta sharePoint para Azure com MABS
description: Utilize o Servidor de Backup Azure para fazer backup e restaurar os seus dados SharePoint. Este artigo fornece as informações para configurar a sua quinta SharePoint para que os dados desejados possam ser armazenados em Azure. Pode restaurar dados protegidos do SharePoint a partir do disco ou do Azure.
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 837aabf739431eebaa6406770620329fe6345eb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375402"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Apoiar uma quinta sharePoint para Azure com MABS

Faz backup de uma quinta SharePoint para o Microsoft Azure utilizando o Microsoft Azure Backup Server (MABS) da mesma forma que faz o backup de outras fontes de dados. O Azure Backup proporciona flexibilidade no calendário de backup para criar pontos de backup diários, semanais, mensais ou mensais e dá-lhe opções de política de retenção para vários pontos de backup. O MABS fornece a capacidade de armazenar cópias de disco locais para objetivos rápidos de tempo de recuperação (RTO) e de armazenar cópias para a Azure para retenção económica e a longo prazo.

O backup do SharePoint para o Azure com o MABS é um processo semelhante para apoiar o SharePoint para o DPM (Data Protection Manager) localmente. Neste artigo serão anotadas considerações específicas para o Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões suportadas no SharePoint e cenários de proteção conexos

Para uma lista de versões do SharePoint suportadas e as versões MABS necessárias para apoiá-las consulte [a matriz de proteção MABS](./backup-mabs-protection-matrix.md)

## <a name="before-you-start"></a>Antes de começar

Há algumas coisas que precisa de confirmar antes de voltar a uma quinta sharePoint para a Azure.

### <a name="whats-not-supported"></a>O que não é suportado

* O MABS que protege uma quinta SharePoint não protege índices de pesquisa ou bases de dados de serviços de aplicações. Terá de configurar a proteção destas bases de dados separadamente.

* O MABS não fornece a cópia de segurança das bases de dados do SharePoint SQL Server que estão hospedadas em ações do servidor de ficheiros de escala (SOFS).

### <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que encontrou todos os [pré-requisitos para usar o Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para proteger as cargas de trabalho. Algumas tarefas para pré-requisitos incluem: criar um cofre de reserva, baixar credenciais de cofre, instalar o Agente de Backup Azure e registar o Azure Backup Server com o cofre.

Pré-requisitos e limitações adicionais:

* Por padrão, quando proteger o SharePoint, todas as bases de dados de conteúdos (e as bases de dados SharePoint_Config e SharePoint_AdminContent*) serão protegidas. Se quiser adicionar personalizações, tais como índices de pesquisa, modelos ou bases de dados de serviço de aplicação, ou o serviço de perfis de utilizador, terá de os configurar separadamente para proteção. Certifique-se de que ativa a proteção para todas as pastas que incluem estes tipos de funcionalidades ou ficheiros de personalização.

* Não pode proteger as bases de dados do SharePoint como origens de dados do SQL Server. Pode recuperar bases de dados individuais a partir da cópia de segurança de um farm.

* Lembre-se que o MABS funciona como **Sistema Local**, e para fazer o back bases de dados do SQL Server precisa de privilégios sysadmin nessa conta para o servidor SQL. No SQL Server pretender fazer o back up, desacrie o NT AUTHORITY\SYSTEM para **sysadmin**.

* Por cada 10 milhões de itens na exploração, deve haver pelo menos 2 GB de espaço no volume onde se encontra a pasta MABS. Este espaço é necessário para a geração do catálogo. Para permitir a utilização do MABS para realizar uma recuperação específica de itens (recolhas de sites, sites, listas, bibliotecas documentais, pastas, documentos individuais e itens de lista), a geração de catálogos cria uma lista dos URLs contidos em cada base de dados de conteúdos. Pode ver a lista de URLs no painel de artigos recuperáveis na área de tarefa de Recuperação da Consola de Administrador MABS.

* Na quinta SharePoint, se tiver bases de dados do SQL Server configuradas com pseudónimos SQL Server, instale os componentes do cliente SQL Server no servidor Web frontal que o MABS protegerá.

* A proteção dos itens de armazenamento da aplicação não é suportada no SharePoint 2013.

* O MABS não suporta a proteção remota do FILESTREAM. O FILESTREAM deve fazer parte da base de dados.

## <a name="configure-backup"></a>Configurar a cópia de segurança

Para fazer o back up da quinta SharePoint, configuure a proteção para o SharePoint utilizando ConfigureSharePoint.exe e, em seguida, crie um grupo de proteção em MABS.

1. **Execute o ConfigureSharePoint.exe** – esta ferramenta configura o Serviço de Escritor VSS do SharePoint \(WSS\) e fornece o agente de proteção com as credenciais para o farm do SharePoint. Depois de ter implantado o agente de proteção, o ficheiro ConfigureSharePoint.exe pode ser encontrado `<MABS Installation Path\>\bin` na pasta no servidor Web \- frontal.  Se tiver vários servidores WFE, só precisa de o instalar num deles. Execute o comando da seguinte forma:

    * No servidor WFE, num pedido de comando, navegue para `\<MABS installation location\>\\bin\\` e `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` corra, onde:

        * **EnableSharePointProtection** permite a proteção da quinta SharePoint, permite ao escritor VSS e regista a identidade da aplicação DCOM WssCmdletsWrapper para funcionar como um utilizador cujas credenciais são inseridas com esta opção. Esta conta deve ser um administrador de farm, bem como um administrador local no Servidor Web front\-end.

        * **EnableSPSearchProtection** ativa a proteção de WSS 3.0 SP Search utilizando a chave de registo SharePointSearchEnumerationEnabled em HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ no Servidor Web front\-end e regista a identidade da aplicação WssCmdletsWrapper do DCOM para ser executada como um utilizador cujas credenciais são introduzidas com esta opção. Esta conta deve ser um administrador de farm, bem como um administrador local no Servidor Web front\-end.

        * **ResolveAllSQLAliases** apresenta todos os aliases comunicados pelo escritor VSS do SharePoint e resolve-os para o SQL Server correspondente. Também apresenta os nomes de instância dos aliases resolvidos. Se os servidores forem espelhados, apresentará também o servidor espelhado. Relata todos os pseudónimos que não estão a ser resolvidos para um SQL Server.

        * **SetTempPath** define as variáveis de ambiente TEMP e TMP para o caminho especificado. A recuperação do nível do item falha se uma grande recolha do site, site, lista ou item estiver sendo recuperado e não houver espaço suficiente na pasta temporária da fazenda. Esta opção permite alterar o caminho de pasta dos ficheiros temporários para um volume que tenha espaço suficiente para armazenar a coleção de sites ou o site que estão a ser recuperados.

    * Introduza as credenciais de administrador do farm. Esta conta deve ser um membro do grupo de Administrador local no servidor WFE. Se o administrador da fazenda não for um administrador local, conceda as seguintes permissões no servidor WFE:

        * Conceder ao **grupo WSS_Admin_WPG** controlo total à pasta MABS ( `%Program Files%\Data Protection Manager\DPM\` ).

        * Conceder ao grupo **WSS_Admin_WPG** leia o acesso à chave do registo MABS ( `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager` ).

        Depois de executar ConfigureSharePoint.exe, terá de a refazer se houver uma alteração nas credenciais de administrador agrícola do SharePoint.

1. Para criar um grupo de proteção, selecione **Protection**  >  **Actions**  >  **Create Protection Group** para abrir o assistente criar novo grupo de **proteção** na consola MABS.

1. Em **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores**.

1. Nos **Membros do Grupo Select,** expanda o servidor que detém o papel de WFE. Se houver mais de um servidor WFE, selecione o que instalou ConfigureSharePoint.exe.

    Quando expande o servidor MABS do SharePoint consulta VSS para ver que dados o MABS pode proteger.  Se a base de dados SharePoint for remota, o MABS liga-se a ela. Se as fontes de dados do SharePoint não aparecerem, verifique se o escritor VSS está a funcionar no servidor SharePoint e em qualquer servidor SQL remoto, e certifique-se de que o agente MABS está instalado tanto no servidor SharePoint como no servidor SQL remoto. Além disso, certifique-se de que as bases de dados do SharePoint não estão a ser protegidas em outros lugares como bases de dados do SQL Server.

1. No **método de proteção de dados Select**, especifique como pretende lidar com a cópia de segurança a curto e longo \- prazo. O \- back up de curto prazo é sempre para dissumir primeiro, com a opção de fazer backup do disco para a nuvem Azure com Azure Backup para curto ou longo \( prazo \- \) .

1. Em **Selecione \- metas de curto prazo**, especifique como pretende voltar ao armazenamento de curto prazo no \- disco.   No **intervalo de retenção,** especifica-se quanto tempo pretende manter os dados no disco. Na **frequência de sincronização,** especifica quantas vezes pretende executar uma cópia de segurança incremental no disco. Se não quiser definir um intervalo de backup, pode verificar antes de um ponto de recuperação para que o MABS execute uma cópia de segurança expressa antes de cada ponto de recuperação ser programado.

1. Na página de atribuição do disco Review, reveja o espaço do disco de armazenamento atribuído ao grupo de proteção.

    **O tamanho total** dos Dados é o tamanho dos dados que pretende fazer e o **espaço do disco a ser a provisionado no MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume em **Detalhes de atribuição do disco**. Para cargas de trabalho, selecione o armazenamento preferido no menu de lista pendente. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Gratuito** no painel **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento que o MABS sugere que adicione ao volume, para continuar com cópias de segurança suavemente no futuro.

1. No **método de criação de réplicas,** selecione como pretende lidar com a replicação completa inicial dos dados.  Se selecionar para replicar a rede, recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou condições de rede não ideais, considere replicar os dados offline com um suporte de dados amovível.

1. Em **Selecionar opções de verificação de consistência**, selecione como pretende automatizar as verificações de consistência. Pode ativar a execução de uma verificação apenas quando os dados de réplica ficarem inconsistentes ou de acordo com uma agenda. Se não quiser configurar a verificação automática de consistência, pode executar uma verificação manual a qualquer momento clicando no grupo de proteção na área de **Proteção** da consola MABS e selecionando **o 'Performance Consistência' Verificado**.

1. Se tiver optado por criar a cópia de segurança para a nuvem com o Azure Backup, na página **Especificar proteção de dados online**, certifique-se de que as cargas de trabalho das quais pretende criar uma cópia de segurança para o Azure estão selecionadas.

1. No **programa de backup on-line,** especifique a frequência com que devem ocorrer backups incrementais para o Azure. Pode agendar as cópias de segurança para serem executadas todos os dias/semanas/meses/anos, bem como a data e hora de execução. As cópias de segurança podem ocorrer, no máximo, duas vezes por dia. Cada vez que uma cópia de segurança é executada, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de backup armazenados no disco MABS.

1. Na **política de retenção on-line,** pode especificar como os pontos de recuperação criados a partir das cópias de segurança diárias/semanais/mensais são mantidos em Azure.

1. In **Escolha a replicação on-line**, especifique como a replicação completa inicial dos dados ocorrerá. Pode replicar através da rede ou criar uma cópia de segurança offline (propagação offline). A cópia de segurança offline utiliza a funcionalidade de Importação do Azure. [Leia mais.](./backup-azure-backup-import-export.md)

1. Na página **Resumo,** reveja as suas definições. Depois de selecionar **Criar Grupo,** ocorre a replicação inicial dos dados. Quando terminar, o estado do grupo de proteção mostrará **como OK** na página **'Status'.** A criação da cópia de segurança está em conformidade com as definições do grupo de proteção.

## <a name="monitoring"></a>Monitorização

Após a criação do grupo de proteção, ocorre a replicação inicial e o MABS começa a fazer o backup e a sincronizar os dados do SharePoint. O MABS monitoriza a sincronização inicial e as cópias de segurança subsequentes.  Pode monitorizar os dados do SharePoint de algumas formas:

* Utilizando a monitorização padrão do MABS, pode configurar notificações para monitorização proactiva, publicando alertas e configurando notificações. Pode enviar notificações por e-mail para alertas críticos, de aviso ou informativos e para o estado de recuperações instanciadas.

* Se utilizar o Gestor de Operações, pode publicar centralmente alertas.

### <a name="set-up-monitoring-notifications"></a>Configurar as notificações de monitorização

1. Na consola de administrador MABS, selecione Opções **de Ação de**  >  **Action**  >  **Monitorização**.

2. Selecione **SMTP Server**, digite o nome do servidor, porta e endereço de e-mail a partir do qual serão enviadas notificações. O endereço tem de ser válido.

3. No **servidor SMTP autenticado,** digite um nome de utilizador e uma palavra-passe. O nome de utilizador e a palavra-passe devem ser o nome da conta de domínio da pessoa cujo endereço "From" é descrito no passo anterior. Caso contrário, a entrega da notificação falha.

4. Para testar as definições do servidor SMTP, selecione **Enviar E-mail**de Teste, digite o endereço de e-mail onde pretende que o MABS envie a mensagem de teste e, em seguida, selecione **OK**. Selecione **Opções**  >  **Notificações** e selecione os tipos de alertas sobre os destinatários que querem ser notificados. Nos **Destinatários** digita o endereço de e-mail de cada destinatário a quem pretende que o MABS envie cópias das notificações.

### <a name="publish-operations-manager-alerts"></a>Publicar alertas do Operations Manager

1. Na consola de administrador MABS, selecione **Opções de Ação de Monitorização**  >  **Action**  >  **Options**  >  **Alert Publishing**  >  **Ative Alerts**

2. Depois de **ativar a Publishing alert,** todos os alertas MABS existentes que possam necessitar de uma ação do utilizador são publicados no registo de **eventos MABS Alerts.** O agente Gestor de Operações que está instalado no servidor MABS publica estes alertas ao Gestor de Operações e continua a atualizar a consola à medida que são gerados novos alertas.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item SharePoint a partir do disco utilizando o MABS

No exemplo seguinte, o *item RecoverIng SharePoint* foi acidentalmente eliminado e precisa de ser recuperado.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra a **consola de administrador MABS**. Todas as explorações SharePoint protegidas pelo MABS são mostradas no **separador Proteção.**

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o item, selecione o **separador Recovery.**

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Pode pesquisar o *sharePoint* para recuperar o item SharePoint utilizando uma pesquisa baseada em wildcard dentro de um intervalo de ponto de recuperação.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriado a partir dos resultados da pesquisa, clique com o botão direito do item e, em seguida, selecione **Recuperar**.
5. Também pode navegar por vários pontos de recuperação e selecionar uma base de dados ou um item para recuperar. Selecione **data > tempo de recuperação**e, em seguida, selecione a base de dados > ponto de **recuperação > ponto de recuperação > > ponto de recuperação do SharePoint**correto .

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Clique com o botão direito no item e, em seguida, **selecione Recuperar** para abrir o Assistente **de Recuperação**. Selecione **Seguinte**.

    ![Seleção de Recuperação de Revisão](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que pretende realizar e, em seguida, selecione **Seguinte**.

    ![Tipo de Recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **Recover to original** no exemplo recupera o item para o site original do SharePoint.
   >
   >
8. Selecione o **Processo de Recuperação** que pretende utilizar.

   * **Selecione Recuperar sem usar uma fazenda de recuperação** se a fazenda SharePoint não tiver mudado e for o mesmo ponto de recuperação que está a ser restaurado.
   * **Selecione Recuperar usando uma fazenda de recuperação** se a fazenda SharePoint tiver mudado desde que o ponto de recuperação foi criado.

     ![Processo de Recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça uma localização de instância do SQL Server para recuperar a base de dados temporariamente e forneça uma partilha de ficheiros de encenação no MABS e no servidor que está a executar o SharePoint para recuperar o item.

    ![Localização de encenação1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    O MABS anexa a base de dados de conteúdos que está a hospedar o item SharePoint à instância temporária do SQL Server. A partir da base de dados de conteúdos, recupera o item e coloca-o na localização do ficheiro de paragem no MABS. O item recuperado que está no local de preparação precisa agora de ser exportado para o local de paragem na fazenda SharePoint.

    ![Localização de encenação2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **Selecione Especificar as opções de recuperação**e aplicar definições de segurança na fazenda SharePoint ou aplicar as definições de segurança do ponto de recuperação. Selecione **Seguinte**.

    ![Opções de Recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Pode optar por acelerar o uso da largura de banda da rede. Isto minimiza o impacto no servidor de produção durante as horas de produção.
    >
    >
11. Reveja as informações de resumo e, em seguida, **selecione Recuperar** para iniciar a recuperação do ficheiro.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecione agora o **separador Monitor na** **Consola de Administrador MABS** para visualizar o **Estado** da recuperação.

    ![Estado de Recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > O ficheiro foi restaurado. Pode refrescar o site do SharePoint para verificar o ficheiro restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Restaurar uma base de dados SharePoint a partir do Azure utilizando o MABS

1. Para recuperar uma base de dados de conteúdos SharePoint, navegue por vários pontos de recuperação (como mostrado anteriormente) e selecione o ponto de recuperação que pretende restaurar.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações disponíveis do catálogo SharePoint.

   > [!NOTE]
   > Como a quinta SharePoint está protegida para retenção a longo prazo no Azure, nenhuma informação de catálogo (metadados) está disponível no servidor MABS. Como resultado, sempre que uma base de dados de conteúdos do SharePoint pontual precisa de ser recuperada, é necessário catalogar novamente a quinta SharePoint.
   >
   >
3. Selecione **Re-catálogo**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de estado **do Recatalog de Nuvem** abre-se.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Depois de concluída a catalogação, o estado muda para *Sucesso*. Selecione **Fechar**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecione o objeto SharePoint mostrado no separador **Recuperação** MABS para obter a estrutura da base de dados de conteúdos. Clique com o botão direito no item e, em seguida, **selecione Recuperar**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste ponto, siga os passos de recuperação mais cedo neste artigo para recuperar uma base de dados de conteúdos SharePoint do disco.

## <a name="switching-the-front-end-web-server"></a>Mudar o servidor web Front-End

Se tiver mais de um servidor web frontal e quiser mudar o servidor que o MABS utiliza para proteger a quinta, siga as instruções:

O procedimento a seguir utiliza o exemplo de uma quinta de servidores com dois servidores Web front-end, *Server1* e *Server2*. O MABS utiliza *o Server1* para proteger a quinta. Altere o servidor Web frontal que o MABS utiliza para o *Server2* para que possa remover o *Server1* da quinta.

> [!NOTE]
> Se o servidor Web frontal que o MABS utiliza para proteger a quinta não estiver disponível, utilize o seguinte procedimento para alterar o servidor Web frontal a partir do passo 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Para alterar o servidor Web frontal que o MABS usa para proteger a quinta

1. Parar o serviço SharePoint VSS Writer no *Server1* executando o seguinte comando num pedido de comando:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. No *Server1,* abra o Editor de Registo e navegue para a seguinte tecla:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Verifique todos os valores listados na sub-chave VssAccessControl. Se qualquer entrada tiver um dado de valor de 0 e outro escritor vss está a correr sob as credenciais de conta associadas, altere os dados de valor para 1.

1. Instale um agente de proteção no *Server2*.

   > [!WARNING]
   > Só é possível mudar os servidores frontais da Web se ambos os servidores estiverem no mesmo domínio.

1. No *Server2,* com um pedido de comando, altere o diretório para `_MABS installation location_\bin\` e executar o **ConfigureSharepoint**. Para obter mais informações sobre o ConfigureSharePoint, consulte [a cópia de segurança Configure](#configure-backup).

1. Selecione o grupo de proteção a que pertence a exploração do servidor e, em seguida, selecione **Modificar o grupo de proteção**.

1. No Assistente de Grupo modificar, na página **'Selecionar Membros do Grupo',** expandir o *Servidor2* e selecionar a fazenda do servidor e, em seguida, completar o assistente.

   Vai começar uma verificação de consistência.

1. Se tiver realizado o passo 6, pode agora retirar o volume do grupo de proteção.

## <a name="next-steps"></a>Passos seguintes

Consulte o artigo [do servidor Back up Exchange.](backup-azure-exchange-mabs.md)
Consulte o artigo [Back up SQL Server.](backup-azure-sql-mabs.md)
