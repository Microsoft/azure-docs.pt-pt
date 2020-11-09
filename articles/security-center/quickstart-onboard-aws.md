---
title: Ligue a sua conta AWS ao Azure Security Center
description: Monitorização dos seus recursos AWS a partir do Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3a2de9b167fcbe9dc603d33fd816e70d5c3705e5
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372783"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Ligue as suas contas AWS ao Azure Security Center

Com cargas de trabalho em nuvem geralmente abrangendo várias plataformas de nuvem, os serviços de segurança na nuvem devem fazer o mesmo.

O Azure Security Center protege cargas de trabalho em Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

A bordo da sua conta AWS no Security Center, integra o AWS Security Hub e o Azure Security Center. O Centro de Segurança proporciona, assim, visibilidade e proteção em ambos os ambientes em nuvem para fornecer:

- Provisionamento automático de agentes (O Centro de Segurança utiliza [o Arco Azure](../azure-arc/servers/overview.md) para implantar o agente Log Analytics nas suas instâncias AWS)
- Gestão de políticas
- Gestão de vulnerabilidades
- Deteção e resposta de ponto final incorporados (EDR)
- Deteção de erros de segurança
- Uma única visão mostrando recomendações do Centro de Segurança e conclusões do AWS Security Hub
- Incorporação dos seus recursos AWS nos cálculos de pontuação segura do Security Center
- Avaliações regulamentares de conformidade dos seus recursos AWS

Na imagem abaixo pode ver as contas AWS apresentadas no painel de visão geral do Security Center.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 projetos GCP listados no painel de visão geral do Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Pré-visualizar<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Proprietário** ou **Colaborador** na assinatura Azure relevante|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||



## <a name="connect-your-aws-account"></a>Ligar a conta AWS

### <a name="step-1-set-up-aws-security-hub"></a>Passo 1. Configurar o Centro de Segurança AWS:

1. Para visualizar recomendações de segurança para várias regiões, repita os seguintes passos para cada região relevante.

    > [!IMPORTANT]
    > Se estiver a utilizar uma conta principal da AWS, repita os três passos seguintes para configurar a conta principal e todas as contas de membros conectados em todas as regiões relevantes

    1. Ativar [a AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Ativar [o centro de segurança AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Verifique se há dados a fluir para o Centro de Segurança.

        Quando ativa pela primeira vez o Security Hub, pode demorar várias horas para os dados estarem disponíveis.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Passo 2. Configurar a autenticação para o Centro de Segurança em AWS

Existem duas formas de permitir que o Centro de Segurança autentica a AWS:

- **Criar uma função IAM para o Centro de Segurança** - Este é o método mais seguro e é recomendado
- **Utilizador AWS para Centro de Segurança** - Uma opção menos segura se não tiver o IAM ativado

#### <a name="create-an-iam-role-for-security-center"></a>Criar um papel IAM para o Centro de Segurança
1. A partir da sua consola Amazon Web Services, em **Segurança, Identidade & Compliance** , selecione **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Serviços AWS":::

1. Selecione **Funções** e **Crie função.**
1. Selecione **outra conta AWS**.
1. Introduza os seguintes detalhes:

    - **ID de conta** - insira o ID da Conta da Microsoft ( **158177204117** ) como mostrado na página de conector AWS no Security Center.
    - **Require External ID** - deve ser selecionado
    - **ID externo** - insira o ID de subscrição como mostrado na página do conector AWS no Centro de Segurança 

1. Selecione **Seguinte**.
1. Na secção **Políticas de Permisse Attach,** selecione as seguintes políticas:

    - SecurityAudit
    - AmazonssmAutomationrole
    - AWSSecurityHubReadOnlyAccess

1. Opcionalmente adicione tags. A adição de Tags ao utilizador não afeta a ligação.
1. Selecione **Seguinte**.

1. Na lista De Papéis, escolha o papel que criou

1. Guarde o Nome de Recursos Amazon (ARN) para mais tarde. 

#### <a name="create-an-aws-user-for-security-center"></a>Criar um utilizador AWS para o Centro de Segurança 
1. Abra o **separador Utilizadores** e selecione **Adicionar utilizador**.
1. Na etapa **Detalhes,** insira um nome de utilizador para Centro de Segurança e certifique-se de que seleciona **o acesso programático** para o Tipo de Acesso AWS. 
1. Selecione **As Próximas Permissões**.
1. **Selecione Fixe as políticas existentes diretamente** e aplique as seguintes políticas:
    - SecurityAudit
    - AmazonssmAutomationrole
    - AWSSecurityHubReadOnlyAccess
    
1. Selecione **Seguinte: Tags**. Opcionalmente adicione tags. A adição de Tags ao utilizador não afeta a ligação.
1. Selecione **Review**.
1. Guarde o ficheiro CSV da **chave** de acesso gerado automaticamente e o ficheiro CSV da **chave de acesso secreto** para mais tarde.
1. Reveja o resumo e clique em **Criar utilizador.**


### <a name="step-3-configure-the-ssm-agent"></a>Passo 3. Configure o Agente SSM

O AWS Systems Manager é necessário para automatizar tarefas através dos seus recursos AWS. Se as suas instâncias EC2 não tiverem o Agente SSM, siga as instruções relevantes da Amazon:

- [Instalação e Configuração do Agente SSM em instâncias do Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Instalação e Configuração do Agente SSM em instâncias Linux Amazon EC2](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Passo 4: Completo Azure Arc pré-requisitos
1. Certifique-se de que os fornecedores de [recursos Azure adequados](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) estão registados:
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Crie um diretor de serviço para o embarque à escala. Como **Proprietário** na subscrição que pretende utilizar para o embarque, crie um principal de serviço para o Azure Arc a bordo, conforme descrito na [Create a Service Principal para o embarque à escala.](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)


### <a name="step-5-connect-aws-to-security-center"></a>Passo 5. Ligue a AWS ao Centro de Segurança

1. A partir do menu do Security Center, selecione **Conectores multi nuvem**.
1. **Selecione adicionar a conta AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Adicione o botão de conta AWS na página de conectores multi nuvem do Security Center":::
1. Configure as opções no **separador de autenticação AWS:**
    1. Introduza um **nome de exibição** para o conector.
    1. Confirme se a subscrição está correta. É a subscrição que incluirá as recomendações do connector e do AWS Security Hub.
    1. Dependendo da opção de autenticação que escolheu no [Passo 2. Configurar a autenticação para o Centro de Segurança em AWS:](#step-2-set-up-authentication-for-security-center-in-aws)
        - **Selecione Assumir Role** e cole o ARN de Criar uma [função IAM para o Centro de Segurança](#create-an-iam-role-for-security-center).
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Colar o ficheiro ARN no campo relevante do assistente de conexão AWS no Portal Azure":::

            OR

        - Selecione **Credenciais** e cole a **chave** de acesso e **a chave secreta** do ficheiro .csv que guardou no Criar um utilizador [AWS para o Centro de Segurança.](#create-an-aws-user-for-security-center)
1. Selecione **Seguinte**.
1. Configure as opções no separador **Configuração Azure Arc:**

    O Security Center descobre as instâncias EC2 na conta AWS conectada e utiliza sSM para embarcar no Arco Azure. 

    > [!TIP]
    > Para a lista de sistemas operativos suportados, veja [quais os sistemas operativos para as minhas instâncias EC2?](#what-operating-systems-for-my-ec2-instances-are-supported)

    1. Selecione o **Grupo de Recursos** e a **Região Azure** para onde os AWS EC2 descobertos serão acedidos na subscrição selecionada.
    1. Insira o **ID principal** de serviço e **o serviço principal cliente secret** para Azure Arc como descrito aqui Criar um diretor de serviço para bordo em [escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Se a máquina estiver a ligar-se à internet através de um servidor proxy, especifique o endereço IP do servidor proxy ou o nome e número de porta que a máquina utiliza para comunicar com o servidor proxy. Insira o valor no formato ```http://<proxyURL>:<proxyport>```
    1. Selecione **Rever + criar**.

        Rever as informações resumidas

        As secções Tags listarão todas as Tags Azure que serão criadas automaticamente para cada EC2 a bordo com os seus próprios detalhes relevantes para reconhecê-lo facilmente em Azure. 

        Saiba mais sobre as etiquetas Azure Tags in [Use para organizar os seus recursos Azure e a hierarquia de gestão.](../azure-resource-manager/management/tag-resources.md)

### <a name="step-7-confirmation"></a>Passo 7. Confirmação

Quando o conector é criado com sucesso e o AWS Security Hub foi configurado corretamente:

- O Security Center analisa o ambiente para instâncias AWS EC2, a bordo do Azure Arc, permitindo instalar o agente Log Analytics e fornecer recomendações de proteção e segurança de ameaças. 
- O serviço ASC verifica novas instâncias AWS EC2 a cada 6 horas e a bordo de acordo com a configuração.
- A norma AWS CIS será apresentada no painel de conformidade regulamentar do Centro de Segurança.
- Se a política do Hub de Segurança estiver ativada, as recomendações aparecerão no portal do Centro de Segurança e no painel de conformidade regulamentar 5-10 minutos após a conclusão do embarque.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Recursos e recomendações da AWS na página de recomendações do Centro de Segurança":::



## <a name="monitoring-your-aws-resources"></a>Monitorizar os seus recursos AWS

Como mostrado acima, a página de recomendações de segurança do Azure Security Center exibe os seus recursos AWS juntamente com os seus recursos Azure e GCP para uma verdadeira visão multi-nuvem.

Para ver todas as recomendações ativas para os seus recursos por tipo de recurso, utilize a página de inventário de ativos do Security Center e filtre para o tipo de recurso AWS em que está interessado:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtro de tipo de recurso da página de inventário de ativos mostrando as opções AWS"::: 


## <a name="aws-in-security-center-faq"></a>AWS no Centro de Segurança FAQ

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Que sistemas operativos para as minhas instâncias EC2 são suportados?

SISTEMA suportado para embarque automático no Arco de Azure para máquinas AWS

- Ubuntu 16.04 - SSM Agent é pré-instalado, por padrão
- Ubuntu 18.04 -SSM Agent está pré-instalado, por padrão
- Servidor windows - SSM Agent está pré-instalado, por padrão
- CentOS Linux 7 – SSM deve ser instalado manualmente ou a bordo separadamente
- SUSE Linux Enterprise Server (SLES) 15 (x64) -SSM deve ser instalado manualmente ou a bordo separadamente
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM deve ser instalado manualmente ou a bordo separadamente


## <a name="next-steps"></a>Passos seguintes

Ligar a sua conta AWS faz parte da experiência multi-nuvem disponível no Azure Security Center. Para obter informações relacionadas, consulte a seguinte página:

- [Ligue as suas contas GCP ao Azure Security Center](quickstart-onboard-gcp.md)
