---
title: Ligue a sua conta GCP ao Azure Security Center
description: Monitorização dos seus recursos GCP a partir do Centro de Segurança Azure
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d5f8278765c3f62fded44e4b89fb5fded6137c94
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757615"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Ligue as suas contas GCP ao Azure Security Center

Com cargas de trabalho em nuvem geralmente abrangendo várias plataformas de nuvem, os serviços de segurança na nuvem devem fazer o mesmo.

O Azure Security Center protege cargas de trabalho em Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

A bordo da sua conta GCP no Centro de Segurança, integra o Comando de Segurança GCP e o Centro de Segurança Azure. O Centro de Segurança proporciona, assim, visibilidade e proteção em ambos os ambientes em nuvem para fornecer:

- Deteção de erros de segurança
- Uma única visão mostrando recomendações do Centro de Segurança e conclusões do Centro de Comando de Segurança GCP
- Incorporação dos seus recursos GCP nos cálculos de pontuação segura do Security Center
- Integração das recomendações do Centro de Comando de Segurança GCP com base na norma CIS no painel de conformidade regulamentar do Centro de Segurança

Na imagem abaixo pode ver projetos GCP apresentados no painel de visão geral do Security Center.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 projetos GCP listados no painel de visão geral do Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Proprietário** ou **Colaborador** na assinatura Azure relevante|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="connect-your-gcp-account"></a>Ligar a conta GCP

Siga os passos abaixo para criar o seu conector de nuvem GCP. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Passo 1. Criar o Centro de Comando de Segurança GCP com Análise de Saúde de Segurança

Para todos os projetos da GCP na sua organização, deve também:

1. Configurar **o Centro de Comando de Segurança GCP** utilizando estas [instruções a partir da documentação do GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Ativar **a Security Health Analytics** utilizando estas [instruções a partir da documentação do GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Verifique se há dados a fluir para o Centro de Comando de Segurança.

As instruções para ligar o ambiente GCP para configuração de segurança seguem as recomendações da Google para consumir recomendações de configuração de segurança. A integração alavanca o Google Security Command Center e irá consumir recursos adicionais que podem afetar a sua faturação.

Quando ativar pela primeira vez o Security Health Analytics, pode demorar várias horas para os dados estarem disponíveis.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Passo 2. Ativar o Centro de Comando de Segurança GCP API

1. A partir da **Biblioteca API** da Consola cloud da Google, selecione o projeto que pretende ligar ao Azure Security Center.
1. Na Biblioteca API, encontre e selecione **o Centro de Comando de Segurança API**.
1. Na página da API, selecione **ENABLE**.

Saiba mais sobre o [Centro de Comando de Segurança API.](https://cloud.google.com/security-command-center/docs/reference/rest/)


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Passo 3. Criar uma conta de serviço dedicada para a integração da configuração de segurança

1. Na **Consola GCP,** selecione o projeto que pretende ligar ao Centro de Segurança.
1. No **menu Navegação**, No IAM & opções **de administração,** selecione **contas de Serviço**.
1. Selecione **CREATE SERVICE ACCOUNT**.
1. Introduza um nome de conta e selecione **Criar**.
1. Especifique a **Função** como **Visualizador de Administração do Centro de Segurança** e selecione **Continue**.
1. O **acesso dos utilizadores grant a esta** secção de conta de serviço é opcional. Selecione **Concluído**.
1. Copie o **valor de e-mail** da conta de serviço criada e guarde-o para posterior utilização.
1. No **menu Navegação**, Opções **de administração & IAM,** selecione **IAM**
    1. Mude para o nível de organização.
    1. Selecione **ADD**.
    1. No campo **Novos membros,** cole o **valor de Email** que copiou anteriormente.
    1. Especifique a Função como **Visualizador de Administração do Centro de Segurança** e, em seguida, selecione Save.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Definição das permissões GCP relevantes":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Passo 4: Criar uma chave privada para a conta de serviço dedicada
1. Mude para o nível de projeto.
1. No **menu Navegação**, No IAM & opções **de administração,** selecione **contas de Serviço**.
1. Abra a conta de serviço dedicada e selecione Editar.
1. Na secção **Chaves,** selecione **ADD KEY** e, em seguida, **Crie uma nova tecla**.
1. No ecrã de teclas privada Create, selecione **JSON** e, em seguida, selecione **CREATE**.
1. Guarde este ficheiro JSON para utilização posterior.


### <a name="step-5-connect-gcp-to-security-center"></a>Passo 5. Ligue o GCP ao Centro de Segurança 
1. A partir do menu do Security Center, selecione **Conectores Cloud**.
1. Selecione adicionar conta GCP.
1. Na página de bordo, faça o seguinte e, em seguida, selecione **Seguinte**.
    1. Validar a subscrição escolhida.
    1. No campo **nome do Visor,** introduza um nome de visualização para o conector.
    1. No campo **de identificação** da Organização, insira a identificação da sua organização. Se não sabe, consulte [criar e gerir organizações.](https://cloud.google.com/resource-manager/docs/creating-managing-organization)
    1. Na caixa de ficheiros **Private,** navegue no ficheiro JSON que descarregou no [Passo 4. Crie uma chave privada para a conta de serviço dedicada.](#step-4-create-a-private-key-for-the-dedicated-service-account)


### <a name="step-6-confirmation"></a>Passo 6. Confirmação

Quando o conector é criado com sucesso e o Centro de Comando de Segurança GCP foi configurado corretamente:

- A norma CEI GCP será apresentada no painel de conformidade regulamentar do Centro de Segurança.
- Recomendações de segurança para os seus recursos GCP aparecerão no portal do Centro de Segurança e no painel de conformidade regulamentar 5-10 minutos após a conclusão do embarque:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="recursos e recomendações do GCP na página de recomendações do Centro de Segurança":::


## <a name="monitoring-your-gcp-resources"></a>Monitorização dos seus recursos GCP

Como mostrado acima, a página de recomendações de segurança do Azure Security Center exibe os seus recursos GCP juntamente com os seus recursos Azure e AWS para uma verdadeira visão multi-nuvem.

Para ver todas as recomendações ativas para os seus recursos por tipo de recurso, utilize a página de inventário de ativos do Security Center e filtre para o tipo de recurso GCP em que está interessado:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtro de tipo de recurso da página de inventário de ativos mostrando as opções de GCP"::: 


## <a name="next-steps"></a>Próximos passos

A ligação da sua conta GCP faz parte da experiência multi-nuvem disponível no Azure Security Center. Para obter informações relacionadas, consulte a seguinte página:

- [Ligue as suas contas AWS ao Azure Security Center](quickstart-onboard-aws.md)
