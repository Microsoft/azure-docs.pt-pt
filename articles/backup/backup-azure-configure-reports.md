---
title: Configurar relatórios para o backup do Azure
description: Configure Power BI relatórios para o backup do Azure usando um cofre dos serviços de recuperação.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: adigan
ms.openlocfilehash: ae772446a4955f9f9def830d5c770f7d826b7e48
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466620"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup
Este artigo mostra as etapas a serem seguidas para configurar relatórios para o backup do Azure usando um cofre dos serviços de recuperação. Ele também mostra como acessar relatórios usando Power BI. Depois de concluir essas etapas, você pode ir diretamente para Power BI para exibir, personalizar e criar relatórios.

> [!IMPORTANT]
> a partir de 1 de Novembro de 2018, é provável que alguns clientes tenham problemas ao carregar os dados na Aplicação Azure Backup no Power BI, obtendo mensagens de erro como, por exemplo, "Encontrámos carateres adicionais no final da entrada JSON. A exceção foi gerada pela interface do IDataReader."
Tal deve-se a uma alteração no formato em que os dados são carregados para a conta de armazenamento.
Baixe o aplicativo mais recente (versão 1,8) para evitar esse problema.
>
>

## <a name="supported-scenarios"></a>Cenários suportados
- Os relatórios de backup do Azure têm suporte para backup de máquina virtual do Azure e backup de arquivo e pasta para a nuvem usando o agente de serviços de recuperação do Azure.
- Não há suporte para relatórios do banco de dados SQL do Azure, compartilhamentos de arquivos do Azure, Data Protection Manager e servidor de backup do Azure no momento.
- Você pode exibir relatórios em cofres e assinaturas, se a mesma conta de armazenamento estiver configurada para cada um dos cofres. A conta de armazenamento selecionada deve estar na mesma região que o cofre dos serviços de recuperação.
- A frequência da atualização agendada para os relatórios é de 24 horas em Power BI. Você também pode executar uma atualização ad hoc dos relatórios no Power BI. Nesse caso, os dados mais recentes na conta de armazenamento do cliente são usados para renderizar relatórios.

## <a name="prerequisites"></a>Pré-requisitos
- Crie uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md) para configurá-la para relatórios. Essa conta de armazenamento é usada para armazenar dados relacionados a relatórios.
- [Crie uma conta de Power bi](https://powerbi.microsoft.com/landing/signin/) para exibir, personalizar e criar seus próprios relatórios usando o portal de Power bi.
- Registre o provedor de recursos **Microsoft.** insights, se ainda não estiver registrado. Use as assinaturas para a conta de armazenamento e o cofre dos serviços de recuperação para que os dados de relatório possam fluir para a conta de armazenamento. Para fazer essa etapa, vá para a Portal do Azure, selecione**provedores de recursos**de **assinatura** > e verifique se este provedor está registrado.

## <a name="configure-storage-account-for-reports"></a>Configurar a conta de armazenamento para relatórios
Siga estas etapas para configurar a conta de armazenamento para um cofre dos serviços de recuperação usando o portal do Azure. Essa é uma configuração única. Depois que a conta de armazenamento é configurada, você pode ir diretamente para Power BI para exibir o pacote de conteúdo e usar relatórios.

1. Se você já tiver um cofre dos serviços de recuperação aberto, vá para a próxima etapa. Se você não tiver um cofre dos serviços de recuperação aberto, na portal do Azure, selecione **todos os serviços**.

   * Na lista de recursos, insira **serviços de recuperação**.
   * À medida que começa a escrever, a lista filtra com base na sua entrada. Quando você vir **cofres dos serviços de recuperação**, selecione-o.
   * É apresentada a lista dos cofres dos Serviços de Recuperação. Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.
2. Na lista de itens que aparece no cofre, na seção **monitoramento e relatórios** , selecione **relatórios de backup** para configurar a conta de armazenamento para relatórios.

      ![Selecionar item de menu de relatórios de backup etapa 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Na folha **relatórios de backup** , selecione o link **configurações de diagnóstico** . Esse link abre a interface do usuário **configurações de diagnóstico** , que é usada para enviar dados por push a uma conta de armazenamento do cliente.

      ![Habilitar diagnósticos etapa 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Selecione **Ativar diagnóstico** para abrir uma interface do usuário a ser usada para configurar uma conta de armazenamento.

      ![Ativar diagnósticos etapa 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Na caixa **nome** , insira um nome de configuração. Marque a caixa de seleção **arquivar em uma conta de armazenamento** para que os dados de relatório possam começar a fluir para a conta de armazenamento.

      ![Habilitar o diagnóstico etapa 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Selecione **conta de armazenamento**, selecione a assinatura relevante e a conta de armazenamento na lista para armazenar dados de relatório e selecione **OK**.

      ![Selecione a conta de armazenamento etapa 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Na seção **log** , marque a caixa de seleção **AzureBackupReport** . Mova o controle deslizante para selecionar um período de retenção para esses dados de relatório. Os dados de relatório na conta de armazenamento são mantidos para o período selecionado usando esse controle deslizante.

      ![Salvar a conta de armazenamento etapa 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Examine todas as alterações e selecione **salvar**. Essa ação garante que todas as suas alterações sejam salvas, e a conta de armazenamento agora está configurada para armazenar dados de relatórios.

9. A tabela de **configurações de diagnóstico** agora mostra a nova configuração habilitada para o cofre. Se não aparecer, atualize a tabela para ver a configuração atualizada.

      ![Exibir a configuração de diagnóstico etapa 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Depois de configurar os relatórios salvando a conta de armazenamento, *Aguarde 24 horas* até que o envio de dados inicial seja concluído. Importe o aplicativo de backup do Azure em Power BI somente após esse período. Para obter mais informações, consulte a [seção perguntas frequentes](backup-azure-monitor-alert-faq.md).
>
>

## <a name="view-reports-in-power-bi"></a>Exibir relatórios no Power BI
Depois de configurar uma conta de armazenamento para relatórios usando um cofre dos serviços de recuperação, leva cerca de 24 horas para que os dados do relatório comecem a fluir. Após 24 horas de configuração de uma conta de armazenamento, siga estas etapas para exibir relatórios em Power BI.
Se você quiser personalizar e compartilhar o relatório, crie um espaço de trabalho e execute as seguintes etapas

1. [Entre no](https://powerbi.microsoft.com/landing/signin/) Power bi.
2. Selecione **Obter Dados**. **Para obter mais maneiras de criar seu próprio conteúdo**, selecione **pacotes de conteúdo de serviço**. Siga as etapas na [documentação do Power bi para se conectar a um serviço](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Na barra de **pesquisa** , insira **backup do Azure** e selecione **obter agora**.

      ![Obter pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-get.png)
4. Insira o nome da conta de armazenamento que foi configurada na etapa 5 anterior e selecione **Avançar**.

    ![Introduza o nome da conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Usando o método de autenticação "chave", insira a chave da conta de armazenamento para esta conta de armazenamento. Para [Exibir e copiar as chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys), acesse sua conta de armazenamento no portal do Azure.

     ![Inserir conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Selecione **entrar**. Depois que a entrada for bem-sucedida, você verá uma notificação de importação de dados.

    ![Importando pacote de conteúdo](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Após a conclusão da importação, você verá uma notificação de **êxito** . Se a quantidade de dados na conta de armazenamento for grande, pode levar um pouco mais para importar o pacote de conteúdo.

    ![Importar pacote de conteúdo com êxito](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Depois que os dados são importados com êxito, o pacote de conteúdo do **backup do Azure** fica visível em **aplicativos** no painel de navegação. Em **painéis**, **relatórios**e **conjuntos**de gráficos, a lista agora mostra o backup do Azure.

8. Em **painéis**, selecione **backup do Azure**, que mostra um conjunto de relatórios de chave fixados.

      ![Painel de backup do Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Para exibir o conjunto completo de relatórios, selecione qualquer relatório no painel.

      ![Integridade do trabalho de backup do Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Selecione cada guia nos relatórios para exibir relatórios nessa área.

      ![Guias de relatórios de backup do Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="troubleshooting-errors"></a>Resolução de erros
| Detalhes do Erro | Resolução |
| --- | --- |
| Depois de configurar a conta de armazenamento para relatórios de backup, a **conta de armazenamento** ainda mostrará **não configurado**. | Se você tiver configurado uma conta de armazenamento com êxito, seus dados de relatório fluirão no apesar desse problema. Para resolver esse problema, vá para o portal do Azure e selecione **todos os serviços** > **configurações** > de diagnóstico**cofre** > de serviços de recuperação**Editar configuração**. Exclua a configuração previamente configurada e crie uma nova configuração na mesma folha. Desta vez, na caixa **nome** , selecione **serviço**. Agora, a conta de armazenamento configurada é exibida. |
|Depois de importar o pacote de conteúdo do backup do Azure no Power BI, uma mensagem de erro "404-contêiner não encontrado" será exibida. | Conforme mencionado anteriormente, você deve aguardar 24 horas depois de configurar relatórios no cofre dos serviços de recuperação para vê-los corretamente no Power BI. Se você tentar acessar os relatórios antes de 24 horas, essa mensagem de erro será exibida porque os dados completos ainda não estão presentes para mostrar relatórios válidos. |

## <a name="next-steps"></a>Passos Seguintes
Depois de configurar a conta de armazenamento e importar o pacote de conteúdo do backup do Azure, as próximas etapas são personalizar relatórios e usar um modelo de dados de relatório para criar relatórios. Para obter mais informações, consulte os artigos a seguir.

* [Usar um modelo de dados de relatório de backup do Azure](backup-azure-reports-data-model.md)
* [Filtrar relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Criar relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
