---
title: Configurar relatórios para o Azure Backup
description: Configure relatórios do Power BI para o Azure Backup com um cofre dos serviços de recuperação.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: adigan
ms.openlocfilehash: dbfe33d062c4609ba29adbaa2d9956687115cb5a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441003"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup
Este artigo mostra os passos a seguir para configurar relatórios para o Azure Backup com um cofre dos serviços de recuperação. Ela também mostra como acessar relatórios com o Power BI. Depois de concluir estes passos, pode aceder diretamente ao Power BI para visualizar, personalizar e criar relatórios.

> [!IMPORTANT]
> a partir de 1 de Novembro de 2018, é provável que alguns clientes tenham problemas ao carregar os dados na Aplicação Azure Backup no Power BI, obtendo mensagens de erro como, por exemplo, "Encontrámos carateres adicionais no final da entrada JSON. A exceção foi gerada pela interface do IDataReader."
Tal deve-se a uma alteração no formato em que os dados são carregados para a conta de armazenamento.
Transfira a aplicação mais recente (versão 1.8) para evitar este problema.
>
>

## <a name="supported-scenarios"></a>Cenários suportados
- Relatórios de cópia de segurança do Azure são suportados para a cópia de segurança da máquina virtual do Azure e arquivo e pasta cópia de segurança para a cloud utilizando o agente de serviços de recuperação do Azure.
- Relatórios para a base de dados do Azure SQL, o partilhas de ficheiros do Azure, o Data Protection Manager e o servidor de cópia de segurança do Azure não são suportados neste momento.
- Pode visualizar relatórios em cofres e subscrições, se a mesma conta de armazenamento está configurada para cada um dos cofres de. A conta de armazenamento selecionada tem de ser na mesma região que o Cofre de serviços de recuperação.
- A frequência de atualização agendada para os relatórios é 24 horas no Power BI. Também pode efetuar uma atualização ad hoc dos relatórios no Power BI. Neste caso, os dados mais recentes na conta de armazenamento de cliente são utilizados para renderizar relatórios.

## <a name="prerequisites"></a>Pré-requisitos
- Criar uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md) para configurá-lo para relatórios. Esta conta de armazenamento é utilizada para armazenar dados relacionados com relatórios.
- [Criar uma conta do Power BI](https://powerbi.microsoft.com/landing/signin/) para visualizar, personalizar e criar seus próprios relatórios através do portal do Power BI.
- Registar o fornecedor de recursos **Microsoft. insights**, se ele não está a ser registado. Utilize as subscrições para a conta de armazenamento e o Cofre dos serviços de recuperação para que os dados de relatórios podem fluir para a conta de armazenamento. Para efetuar este passo, aceda ao portal do Azure, selecione **subscrição** > **fornecedores de recursos**e verificar este fornecedor de registá-lo.

## <a name="configure-storage-account-for-reports"></a>Configurar a conta de armazenamento para relatórios
Siga estes passos para configurar a conta de armazenamento para um cofre dos serviços de recuperação com o portal do Azure. Esta é uma configuração única. Depois da conta de armazenamento está configurada, pode ir diretamente para o Power BI para ver o pacote de conteúdos e utilizar relatórios.

1. Se já tiver um cofre de serviços de recuperação aberto, avance para o passo seguinte. Se não tiver um cofre de serviços de recuperação aberto, no portal do Azure, selecione **todos os serviços**.

   * Na lista de recursos, introduza **serviços de recuperação**.
   * À medida que começa a escrever, a lista filtra com base na sua entrada. Quando vir **cofres dos serviços de recuperação**, selecioná-lo.

      ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

   * É apresentada a lista dos cofres dos Serviços de Recuperação. Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.
2. Na lista de itens que aparece em sob o cofre, o **monitorização e relatórios** secção, selecione **relatórios de cópia de segurança** para configurar a conta de armazenamento para relatórios.

      ![Selecione os relatórios de cópia de segurança menu item passo 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Sobre o **relatórios de cópia de segurança** painel, selecione a **as definições de diagnóstico** ligação. Esse link abre o **as definições de diagnóstico** interface do Usuário, que é utilizada para enviar dados por push para uma conta de armazenamento do cliente.

      ![Ative o diagnóstico passo 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Selecione **ativar os diagnósticos** para abrir uma interface do Usuário para utilizar para configurar uma conta de armazenamento.

      ![Ativar o passo de diagnóstico 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Na **nome** , introduza um nome de definição. Selecione o **arquivo para uma conta de armazenamento** caixa de verificação para que os relatórios de dados podem começar a ser encaminhados para a conta de armazenamento.

      ![Ative o diagnóstico passo 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Selecione **conta de armazenamento**, selecione a subscrição relevante e a conta de armazenamento na lista para armazenar dados de relatórios e selecione **OK**.

      ![Selecione o passo de conta de armazenamento 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Sob o **Log** secção, selecione a **AzureBackupReport** caixa de verificação. Mova o controlo de deslize para selecionar um período de retenção de dados de relatórios. Relatórios de dados na conta de armazenamento é mantido durante o período selecionado utilizando este controlo de deslize.

      ![Guardar o passo de conta de armazenamento 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Reveja todas as alterações e selecione **guardar**. Esta ação garante que todas as alterações são guardadas e a conta de armazenamento está agora configurada para armazenar dados de relatórios.

9. O **das definições de diagnóstico** tabela mostra agora a nova definição ativada para o cofre. Se não aparecer, atualize a tabela para ver a definição atualizada.

      ![Passo 9 da definição de diagnóstico do Vista](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Depois de configurar relatórios ao guardar a conta de armazenamento *Aguarde 24 horas* para o envio de dados inicial concluir. Importe a aplicação de cópia de segurança do Azure no Power BI apenas após esse tempo. Para obter mais informações, consulte a [secção de FAQ](#frequently-asked-questions).
>
>

## <a name="view-reports-in-power-bi"></a>Ver relatórios no Power BI
Depois de configurar uma conta de armazenamento para relatórios utilizando um cofre dos serviços de recuperação, demora cerca de 24 horas para dados de relatórios começar a fluir em. Após 24 horas de configuração de uma conta de armazenamento, siga estes passos para ver relatórios no Power BI.
Se desejar personalizar e partilhar o relatório, criar uma área de trabalho e siga os passos abaixo

1. [Inicie sessão no](https://powerbi.microsoft.com/landing/signin/) para o Power BI.
2. Selecione **Obter Dados**. Na **mais formas de criar o seu conteúdo**, selecione **pacotes de conteúdos do serviço**. Siga os passos a [documentação do Power BI para ligar a um serviço](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Na **pesquisa** barra, introduza **cópia de segurança do Azure** e selecione **obter agora**.

      ![Obter pacote de conteúdos](./media/backup-azure-configure-reports/content-pack-get.png)
4. Introduza o nome da conta de armazenamento que foi configurada no passo anterior, 5 e selecione **seguinte**.

    ![Introduza o nome da conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Usando o método de autenticação "Chave", introduza a chave de conta de armazenamento para esta conta de armazenamento. Para [ver e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys), aceda à sua conta de armazenamento no portal do Azure.

     ![Introduza a conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Selecione **iniciar sessão**. Depois de início de sessão for bem sucedida, verá uma notificação de dados de importação.

    ![Importar o pacote de conteúdos](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Depois de concluída a importação, verá uma **êxito** notificação. Se a quantidade de dados na conta de armazenamento for grande, poderá demorar um pouco mais para importar o pacote de conteúdos.

    ![Importar o pacote de conteúdos de sucesso](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Depois de importar de dados com êxito, o **Azure Backup** pacote de conteúdos é visível na **aplicações** no painel de navegação. Sob **Dashboards**, **relatórios**, e **conjuntos de dados**, a lista mostra agora a cópia de segurança do Azure.

8. Sob **Dashboards**, selecione **cópia de segurança do Azure**, que mostra um conjunto de afixado relatórios chave.

      ![Dashboard de cópia de segurança do Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Para ver o conjunto completo de relatórios, selecione qualquer relatório no dashboard.

      ![Integridade de tarefa de cópia de segurança do Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Selecione cada separador nos relatórios para ver os relatórios nessa área.

      ![Separadores de relatórios de cópia de segurança do Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Como posso ver se os dados de relatórios foi iniciada a ser encaminhados para uma conta de armazenamento?
Vá para a conta de armazenamento que configurou e selecionar contentores. Se o contentor tem uma entrada para insights-logs-azurebackupreport, ele indica que dados de relatórios foi iniciada a fluir.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>O que é a frequência de push de dados para uma conta de armazenamento e o pacote de conteúdos de cópia de segurança do Azure no Power BI?
  Para os utilizadores de dia 0, demora cerca de 24 horas para enviar dados para uma conta de armazenamento. Após a conclusão deste push inicial, os dados são atualizados com frequência mostrada na figura a seguir.

  * Dados relacionados com **trabalhos**, **alertas**, **itens de cópia de segurança**, **cofres**, **servidores protegidos**e  **Políticas** são emitidos via push para uma conta de armazenamento do cliente como e quando é registado.

  * Dados relacionados com **armazenamento** são emitidos via push para uma conta de armazenamento do cliente a cada 24 horas.

       ![Frequência de push de dados de relatórios de cópia de segurança do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * O Power BI tem um [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Pode efetuar uma atualização manual dos dados no Power BI para o pacote de conteúdos.

### <a name="how-long-can-i-retain-reports"></a>Quanto posso manter relatórios?
Ao configurar uma conta de armazenamento, pode selecionar um período de retenção de dados do relatório na conta de armazenamento. Siga etapa 6 a [configurar a conta de armazenamento para relatórios](backup-azure-configure-reports.md#configure-storage-account-for-reports) secção. Também pode [analise relatórios no Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvá-los por um período de retenção mais longo, com base nas suas necessidades.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Irei ver todos os meus dados em relatórios depois de configurar a conta de armazenamento?
 Todos os dados gerados depois de configurar uma conta de armazenamento são emitidos via push para a conta de armazenamento e está disponíveis nos relatórios. Tarefas em curso não são enviados por push para relatórios. Depois da tarefa é concluída ou falha, ele é enviado para relatórios.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Se eu já configurado a conta de armazenamento para ver relatórios, posso alterar a configuração para utilizar outra conta de armazenamento?
Sim, pode alterar a configuração para apontar para uma conta de armazenamento diferente. Utilize a conta de armazenamento recentemente configurado enquanto se conectar ao pacote de conteúdos do Azure Backup. Além disso, após a configuração de uma conta de armazenamento diferentes, novos fluxos de dados nesta conta de armazenamento. Os dados mais antigos (antes de alterar a configuração) ainda permanecem na conta de armazenamento mais antiga.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Pode visualizar relatórios em cofres e subscrições?
Sim, pode configurar a mesma conta de armazenamento em vários cofres para ver relatórios de cross-cofre. Além disso, pode configurar a mesma conta de armazenamento para cofres entre subscrições. Em seguida, pode utilizar esta conta de armazenamento enquanto se conectar ao pacote de conteúdos de cópia de segurança do Azure no Power BI para visualizar os relatórios. A conta de armazenamento selecionada tem de ser na mesma região que o Cofre de serviços de recuperação.

## <a name="troubleshooting-errors"></a>Resolução de erros
| Detalhes do Erro | Resolução |
| --- | --- |
| Depois de configurar a conta de armazenamento para relatórios de cópia de segurança, **conta de armazenamento** continua a mostrar **não configurada**. | Se tiver configurado uma conta de armazenamento com êxito, os dados de relatórios fluem apesar deste problema. Para resolver este problema, aceda ao portal do Azure e selecione **todos os serviços** > **as definições de diagnóstico** > **cofre dos serviços de recuperação**  >  **Editar definição**. Eliminar a definição configurada anteriormente e criar uma nova definição no painel do mesmo. Desta vez, além da **Name** caixa, selecione **serviço**. Agora é apresentada a conta de armazenamento configurada. |
|Depois de importar o pacote de conteúdos de cópia de segurança do Azure no Power BI, um erro "404-container não é encontrado" é apresentada a mensagem. | Como mencionado anteriormente, tem de aguardar 24 horas depois de configurar relatórios no cofre dos serviços de recuperação para vê-los corretamente no Power BI. Se tentar aceder aos relatórios antes de 24 horas, é apresentada esta mensagem de erro pois completa de dados ainda não está presente para mostrar relatórios válidos. |

## <a name="next-steps"></a>Passos Seguintes
Depois de configurar a conta de armazenamento e importar o pacote de conteúdos de cópia de segurança do Azure, são os passos seguintes para personalizar os relatórios e utilizar um modelo de dados de relatórios para criar relatórios. Para obter mais informações, consulte os artigos seguintes.

* [Utilizar um modelo de dados de relatórios do Azure Backup](backup-azure-reports-data-model.md)
* [Filtro de relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Criar relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
