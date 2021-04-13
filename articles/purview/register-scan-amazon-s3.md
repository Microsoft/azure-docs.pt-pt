---
title: Como digitalizar baldes Amazon S3
description: Este guia de como fazer descreve detalhes de como digitalizar baldes Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: a0559028192b0a99aeffd45a3b2896f9c9d159be
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310206"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Conector Azure Purview para Amazon S3

Este guia de como fazer fornece uma explicação de como usar o Azure Purview para digitalizar os seus dados não estruturados atualmente armazenados em baldes padrão Amazon S3 e descobrir que tipos de informação sensível existem nos seus dados. Este guia também descreve como identificar os Baldes Amazon S3 onde os dados são atualmente armazenados para facilitar a proteção de informação e conformidade com os dados.

Para este serviço, utilize o Purview para fornecer uma conta microsoft com acesso seguro à AWS, onde o scanner Desreserva será executado. O scanner de Alças utiliza este acesso aos baldes do Amazon S3 para ler os seus dados e, em seguida, reporta os resultados da digitalização, incluindo apenas os metadados e classificação, de volta ao Azure. Utilize os relatórios de classificação e rotulagem do Purview para analisar e rever os resultados da sua verificação de dados.

Neste guia de como fazer, você vai aprender sobre como adicionar baldes Amazon S3 como recursos de Purga e criar uma varredura para os seus dados Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Âmbito de visão para Amazon S3

O seguinte âmbito é específico para registar e digitalizar baldes Amazon S3 como fontes de dados do Purview.

|Âmbito  |Descrição  |
|---------|---------|
|**Limites de dados**     |    O serviço de scanner de Purview suporta atualmente a digitalização de baldes Amazon S3 para até 100 GB de dados por inquilino.     |
|**Tipos de ficheiros**     | O serviço de scanner Purview suporta atualmente os seguintes tipos de ficheiros: <br><br>.avro, .csv, .doc, .docm, .docx, .dot, .json, .odp, .ods, .odt, .orc, .parquet, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .psv, .ssv, .tsv, .txt, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlt, .xml        |
|**Regiões**     | O conector de Purview para o serviço Amazon S3 está atualmente implantado apenas nas regiões **AWS US East (Ohio)** e **Europa (Frankfurt).** <br><br>Para obter mais informações, consulte [as regiões de Armazenamento e Digitalização.](#storage-and-scanning-regions)   |
|     |         |

Para obter mais informações, consulte os limites de Competência documentados em:

- [Gerir e aumentar quotas para recursos com a Azure Purview](how-to-manage-quotas.md)
- [Fontes de dados suportadas e tipos de ficheiros em Azure Purview](sources-and-scans.md)
- [Use pontos finais privados para a sua conta Purview](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Regiões de armazenamento e digitalização

A tabela que se segue mapeia as regiões onde os dados são armazenados na região onde seriam digitalizados pela Azure Purview.

> [!IMPORTANT]
> Os clientes serão cobrados por todos os encargos relacionados de transferência de dados de acordo com a região do seu balde.
>

| Região de armazenamento | Região de digitalização |
| ------------------------------- | ------------------------------------- |
| Leste dos EUA (Ohio)                  | Leste dos EUA (Ohio)                        |
| Us East (N. Virgínia)           | EUA Leste (Ohio) ou EUA Leste (N. Virgínia)                       |
| EUA Oeste (N. Califórnia)         | Leste dos EUA (Ohio)                        |
| E.U.A. Oeste (Oregon)                | Leste dos EUA (Ohio)                        |
| África (Cidade do Cabo)              | Europa (Frankfurt)                    |
| Ásia-Pacífico (Hong Kong)        | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                   |
| Ásia-Pacífico (Mumbai)           | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                   |
| Ásia-Pacífico (Osaka-Local)      | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                   |
| Ásia-Pacífico (Seul)            | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                   |
| Ásia-Pacífico (Singapura)        | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                   |
| Ásia-Pacífico (Sydney)           | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                  |
| Ásia-Pacífico (Tóquio)            | Europa (Frankfurt) ou Ásia-Pacífico (Sydney)                 |
| Canadá (Central)                | Leste dos EUA (Ohio)                        |
| China (Pequim)                 | Não suportado                    |
| China (Ningxia)                 | Não suportado                   |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Irlanda)                | Europa (Frankfurt) ou Europa (Irlanda)                   |
| Europa (Londres)                 | Europa (Frankfurt) ou Europa (Irlanda)                   |
| Europa (Milão)                  | Europa (Frankfurt)                    |
| Europa (Paris)                  | Europa (Frankfurt)                    |
| Europa (Estocolmo)              | Europa (Frankfurt)                    |
| Médio Oriente (Bahrein)           | Europa (Frankfurt)                    |
| América do Sul (São Paulo)       | Leste dos EUA (Ohio)                        |
| | |

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que realizou os seguintes pré-requisitos antes de adicionar os seus baldes Amazon S3 como fontes de dados do Purview e digitalizar os seus dados S3.

> [!div class="checklist"]
> * Você precisa ser um Azure Purview Data Source Admin.
> * [Crie uma conta Desview](#create-a-purview-account) se ainda não tiver uma
> * [Crie uma credencial de provisão para o seu scan de balde AWS](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Criar uma nova função AWS para uso com a Purview](#create-a-new-aws-role-for-purview)
> * [Configurar a pesquisa para baldes Amazon S3 encriptados,](#configure-scanning-for-encrypted-amazon-s3-buckets)se relevantes
> * Ao adicionar os seus baldes como recursos de Purga, você precisará dos valores do seu [AWS ARN,](#retrieve-your-new-role-arn)nome do [balde](#retrieve-your-amazon-s3-bucket-name)e, por vezes, do seu [ID de conta AWS](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Criar uma conta Desview

- **Se já tiver uma conta Purview,** pode continuar com as configurações necessárias para o suporte AWS S3. Comece por [Criar uma credencial de provisão para a sua varredura de balde AWS](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Se precisar de criar uma conta Desres visão,** siga as instruções na [Configuração de uma conta Azure Purview](create-catalog-portal.md). Depois de criar a sua conta, volte aqui para completar a configuração e comece a usar o conector Purview para o Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Crie uma credencial de provisão para o seu scan de balde AWS

Este procedimento descreve como criar uma nova credencial de visão para usar ao digitalizar os baldes AWS.

> [!TIP]
> Também pode criar uma nova credencial no meio do processo, enquanto [configura a sua digitalização](#create-a-scan-for-one-or-more-amazon-s3-buckets). Nesse caso, no campo **credencial,** selecione **New**.
>

1. Em Purview, navegue para o **Centro de Gestão,** e em **Segurança e acesso,** selecione **Credenciais**.

1. Selecione **Novo**, e no novo painel **de credenciais** que aparece à direita, use os seguintes campos para criar a sua credencial Purview:

    |Campo |Descrição  |
    |---------|---------|
    |**Nome**     |Introduza um nome significativo para esta credencial, ou use o padrão.        |
    |**Descrição**     |Introduza uma descrição opcional para esta credencial, como `Used to scan the tutorial S3 buckets`         |
    |**Método de autenticação**     |Selecione **Role ARN**, uma vez que está a usar um ARN de papel para aceder ao seu balde.         |
    |**ID da conta da Microsoft**     |Clique para copiar este valor para a área de transferência. Utilize este valor como **ID** da conta da Microsoft ao [criar o seu Role ARN em AWS](#create-a-new-aws-role-for-purview).           |
    |**ID externo**     |Clique para copiar este valor para a área de transferência. Utilize este valor como **ID Externo** ao [criar o seu ARN de papel em AWS.](#create-a-new-aws-role-for-purview)        |
    |**Função ARN**     | Assim que [tiver criado o seu papel amazon IAM,](#create-a-new-aws-role-for-purview)navegue para o seu papel na área do IAM, copie o valor **Role ARN** e insira-o aqui. Por exemplo: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Para mais informações, consulte [Recuperar o seu novo Role ARN](#retrieve-your-new-role-arn). |
    | | |

    **Selecione Criar** quando terminar de criar a credencial.

1. Se ainda não o fez, copie e cole os valores **de ID** da conta da Microsoft e **de ID Externo** para utilização ao criar uma nova [função AWS para o Purview](#create-a-new-aws-role-for-purview), que é o seu próximo passo.

Para obter mais informações sobre as credenciais de Competência, consulte [as credenciais para autenticação de origem em Azure Purview](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Criar um novo papel AWS para a Purga

Este procedimento requer que introduza os valores para o seu ID de Conta Azure e ID Externo ao criar a sua função AWS.

Se não tiver estes valores, localize-os primeiro na sua [credencial de Purview](#create-a-purview-credential-for-your-aws-bucket-scan).

**Para localizar o seu ID de Conta microsoft e iD externo:**

1. Em Purview, navegue para o Centro de Segurança de **Gestão**  >  **e**  >  **aceda às Credenciais** de Acesso.

1. Selecione a credencial que criou para a sua verificação do [balde AWS](#create-a-purview-credential-for-your-aws-bucket-scan)e, em seguida, na barra de ferramentas, selecione **Editar**.

1. No painel **de credenciais de edição** que aparece à direita, copie os valores **de ID** de conta da Microsoft e **iD externo** para um ficheiro separado, ou faça-os à mão para colar no campo relevante em AWS.

    Por exemplo:

    [![Localize os valores de ID da sua conta microsoft e valores de ID Externo. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Para criar o seu papel AWS para a Purga:**

1.  Abra a sua consola **Amazon Web Services,** e em **Segurança, Identidade e Conformidade,** selecione **IAM**.

1. Selecione **Funções** e, em seguida, **Crie função**.

1. Selecione **outra conta AWS** e, em seguida, introduza os seguintes valores:

    |Campo  |Descrição  |
    |---------|---------|
    |**ID da Conta**     |    Introduza o seu ID da conta microsoft. Por exemplo: `615019938638`     |
    |**ID externo**     |   Em opções, **selecione Requerer ID externo...** e, em seguida, insira o seu ID Externo no campo designado. <br>Por exemplo: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Por exemplo:

    ![Adicione o ID da conta microsoft à sua conta AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Na **função Criar > Fixe as políticas de permissões,** filtra as permissões apresentadas ao **S3**. Selecione **AmazonS3ReadOnlyAccess** e, em seguida, selecione **Seguinte: Tags**.

    ![Selecione a política ReadOnlyAccess para o novo papel de digitalização do Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > A política **AmazonS3ReadOnlyAccess** fornece permissões mínimas necessárias para digitalizar os baldes S3, podendo incluir outras permissões também.
    >
    >Para aplicar apenas as permissões mínimas necessárias para digitalizar os seus baldes, crie uma nova política com as permissões listadas nas [permissões Mínimas para a sua política AWS](#minimum-permissions-for-your-aws-policy), dependendo se pretende digitalizar um único balde ou todos os baldes da sua conta. 
    >
    >Aplique a sua nova política no papel em vez de **AmazonS3ReadOnlyAccess.**

1. Na área **de adicionar tags (opcional),** pode optar opcionalmente por criar uma etiqueta significativa para este novo papel. As etiquetas úteis permitem organizar, rastrear e controlar o acesso a cada papel que criar.

    Introduza uma nova chave e valor para a sua etiqueta, conforme necessário. Quando terminar, ou se quiser saltar este passo, selecione **Seguinte: Reveja** os detalhes da função e complete a criação de funções.

    ![Adicione uma etiqueta significativa para organizar, rastrear ou controlar o acesso para o seu novo papel.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Na área **de Revisão,** faça o seguinte:

    - No campo **de nome role,** insira um nome significativo para o seu papel
    - Na caixa **de descrição do papel,** introduza uma descrição opcional para identificar o propósito da função
    - Na secção **Políticas,** confirme que a política correta **(AmazonS3ReadOnlyAccess**) está ligada ao papel.

    Em seguida, **selecione Criar função** para concluir o processo.

    Por exemplo:

    ![Reveja detalhes antes de criar o seu papel.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Configurar a pesquisa para baldes Criptografados Amazon S3

Os baldes AWS suportam vários tipos de encriptação. Para baldes que utilizam encriptação **AWS-KMS,** é necessária uma configuração especial para permitir a digitalização.

> [!NOTE]
> Para baldes que não utilizem encriptação, encriptação AES-256 ou AWS-KMS S3, salte esta secção e continue a [recuperar o seu nome de balde Amazon S3](#retrieve-your-amazon-s3-bucket-name).
>

**Para verificar o tipo de encriptação utilizada nos seus baldes Amazon S3:**

1. Em AWS, navegue para **o armazenamento**  >  **S3** > e selecione **Baldes** do menu à esquerda.

    ![Selecione o separador Amazon S3 Buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Selecione o balde que pretende verificar. Na página de detalhes do balde, selecione o **separador Propriedades** e desloque-se para a área **de encriptação padrão.**

    - Se o balde selecionado estiver configurado para qualquer coisa menos encriptação **AWS-KMS,** incluindo se a encriptação padrão para o seu balde estiver **desativada,** salte o resto deste procedimento e continue com o nome do [balde Amazon S3](#retrieve-your-amazon-s3-bucket-name).

    - Se o balde selecionado estiver configurado para encriptação **AWS-KMS,** continue como descrito abaixo para adicionar uma nova política que permita digitalizar um balde com encriptação **AWS-KMS** personalizada.

    Por exemplo:

    ![Veja um balde Amazon S3 configurado com encriptação AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Para adicionar uma nova política para permitir a digitalização de um balde com encriptação AWS-KMS personalizada:**

1. Na AWS, navegue para as Políticas IAM **dos Serviços**  >     >   e selecione **Criar a política.**

1. No **separador De** editor de política Criar Política  >  **Visual,** defina a sua política com os seguintes valores:

    |Campo  |Descrição  |
    |---------|---------|
    |**Serviço**     |  Introduza e selecione **KMS**.       |
    |**Ações**     | No **nível de Acesso,** selecione **Write** para expandir a secção **Escrever.**<br>Uma vez expandido, selecione apenas a opção **Desencriptado.**        |
    |**Recursos**     |Selecione um recurso específico ou **todos os recursos.**         |
    | | |

    Quando terminar, selecione **a política de revisão** para continuar.

    ![Crie uma política para digitalizar um balde com encriptação AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Na página de política de **Revisão,** insira um nome significativo para a sua política e uma descrição opcional e, em seguida, selecione **Criar a política**.

    A política recém-criada é adicionada à sua lista de políticas.

1. Anexe a sua nova política ao papel que adicionou para digitalização.

    1. Volte para a página **IAM**  >  **Roles** e selecione o papel que adicionou [anteriormente](#create-a-new-aws-role-for-purview).

    1. No separador **Permissões,** **selecione Políticas de anexação**.

        ![No separador Permissões da sua função, selecione Políticas de anexação.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Na página **'Permissões' 'Anexar',** procure e selecione a nova política que criou acima. **Selecione A política de anexar** a sua política ao papel.

        A página **Resumo** é atualizada, com a sua nova política ligada ao seu papel.

        ![Consulte uma página de Resumo atualizada com a nova política anexa à sua função.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Recupere o seu novo Role ARN

Você precisará gravar o seu AWS Role ARN e copiá-lo para o Purview ao [criar uma digitalização para o seu balde Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**Para recuperar o seu papel ARN:**

1. Na área de Funções de **Gestão de Identidade e Acesso (IAM)** da AWS,  >   procure e selecione o novo papel que [criou para a Purview.](#create-a-purview-credential-for-your-aws-bucket-scan)

1. Na página **Resumo** da função, selecione o botão **Copy para clipboard** à direita do valor Role **ARN.**

    ![Copie o valor ARN da função para a área de transferência.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Cole este valor num local seguro, pronto a usar ao [criar uma varredura para o seu balde Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Recupere o seu nome de balde Amazon S3

Você precisará do nome do seu balde Amazon S3 para copiá-lo em Purview ao [criar uma digitalização para o seu balde Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Para recuperar o seu nome de balde:**

1. Em AWS, navegue para **o armazenamento**  >  **S3** > e selecione **Baldes** do menu à esquerda.

    ![Veja o separador Amazon S3 Buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Procure e selecione o seu balde para ver a página de detalhes do balde e, em seguida, copie o nome do balde para a área de transferência.

    Por exemplo:

    ![Recupere e copie a URL do balde S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Cole o nome do balde num ficheiro seguro e `s3://` adicione-lhe um prefixo para criar o valor que terá de introduzir ao configurar o seu balde como recurso Purview.

    Por exemplo: `s3://purview-tutorial-bucket`

> [!NOTE]
> Apenas o nível de raiz do seu balde é suportado como uma fonte de dados purview. Por exemplo, não *é* suportado o seguinte URL, que inclui uma sub-pasta: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Localize o seu ID de conta AWS

Você precisará do seu ID da conta AWS para registar a sua conta AWS como uma fonte de dados Purview, juntamente com todos os seus baldes.

O ID da sua conta AWS é o ID que utiliza para iniciar sessão na consola AWS. Também pode encontrá-lo assim que estiver iniciado no painel de instrumentos do IAM, à esquerda sob as opções de navegação, e no topo, como a parte numérica do seu URL de login:

Por exemplo:

![Recupere o seu ID da conta AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Adicione um único balde Amazon S3 como um recurso Purview

Utilize este procedimento se tiver apenas um único balde S3 que pretende registar no Purview como fonte de dados, ou se tiver vários baldes na sua conta AWS, mas não quiser registar todos eles no Purview.

**Para adicionar o seu balde:** 

1. Lance o portal Purview utilizando o conector de alças dedicado para o URL Amazon S3. Este URL foi-lhe fornecido pela equipa de gestão de produtos de conector Da Amazon S3 Purview.

    ![Lançar o portal Desemview.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navegue na página Azure Purview **Sources** e selecione O ícone **registo** de ![ registos.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Continuar.**

    ![Adicione um balde Amazon AWS como fonte de dados de Purview.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Se tiver [várias coleções](manage-data-sources.md#manage-collections) e quiser adicionar o seu Amazon S3 a uma coleção específica, selecione a **vista do Mapa** no topo direito e, em seguida, selecione o ícone **Registo** ![ registador.](./media/register-scan-amazon-s3/register-button.png) botão dentro da sua coleção.
    >

1. No **painel de fontes de registo (Amazon S3)** que abre, insira os seguintes detalhes:

    |Campo  |Descrição  |
    |---------|---------|
    |**Nome**     |Introduza um nome significativo, ou use o padrão fornecido.         |
    |**URL do balde**     | Introduza o URL do balde AWS, utilizando a seguinte sintaxe:   `s3://<bucketName>`     <br><br>**Nota:** Certifique-se de que utiliza apenas o nível de raiz do seu balde, sem sub-pastas. Para mais informações, consulte [o nome do seu balde Amazon S3.](#retrieve-your-amazon-s3-bucket-name) |
    |**Selecione uma coleção** |Se selecionou para registar uma fonte de dados dentro de uma recolha, essa recolha já está listada. <br><br>Selecione uma coleção diferente conforme necessário, **nenhuma** para atribuir nenhuma coleção, ou **New** para criar uma nova coleção agora. <br><br>Para obter mais informações sobre as coleções de Purview, consulte [Gerir fontes de dados em Azure Purview.](manage-data-sources.md#manage-collections)|
    | | |

    Quando terminar, **selecione Finish** para completar o registo.

Continue com [Crie uma varredura para um ou mais baldes Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Adicione uma conta Amazon como um recurso de visão

Utilize este procedimento se tiver vários baldes S3 na sua conta Amazon, e pretende registar todos eles como fontes de dados purview.

Ao [configurar a sua digitalização,](#create-a-scan-for-one-or-more-amazon-s3-buckets)poderá selecionar os baldes específicos que pretende digitalizar, se não quiser digitalizá-los todos juntos.

**Para adicionar a sua conta Amazon:**
1. Lance o portal Purview utilizando o conector de alças dedicado para o URL Amazon S3. Este URL foi-lhe fornecido pela equipa de gestão de produtos de conector Da Amazon S3 Purview.

    ![Launch Connector para o portal dedicado à Visão dedicada da Amazon S3](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navegue na página Azure Purview **Sources** e selecione O ícone **registo** de ![ registos.](./media/register-scan-amazon-s3/register-button.png) > **Contas da**  >  Amazon **Continuar.**

    ![Adicione uma conta Amazon como fonte de dados de Purview.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Se tiver [várias coleções](manage-data-sources.md#manage-collections) e quiser adicionar o seu Amazon S3 a uma coleção específica, selecione a **vista do Mapa** no topo direito e, em seguida, selecione o ícone **Registo** ![ registador.](./media/register-scan-amazon-s3/register-button.png) botão dentro da sua coleção.
    >

1. No **painel de fontes de registo (Amazon S3)** que abre, insira os seguintes detalhes:

    |Campo  |Descrição  |
    |---------|---------|
    |**Nome**     |Introduza um nome significativo, ou use o padrão fornecido.         |
    |**ID da conta AWS**     | Insira o seu ID da conta AWS. Para mais informações, consulte [localizar o seu ID de conta AWS](#locate-your-aws-account-id)|
    |**Selecione uma coleção** |Se selecionou para registar uma fonte de dados dentro de uma recolha, essa recolha já está listada. <br><br>Selecione uma coleção diferente conforme necessário, **nenhuma** para atribuir nenhuma coleção, ou **New** para criar uma nova coleção agora. <br><br>Para obter mais informações sobre as coleções de Purview, consulte [Gerir fontes de dados em Azure Purview.](manage-data-sources.md#manage-collections)|
    | | |

    Quando terminar, **selecione Finish** para completar o registo.

Continue com [Crie uma varredura para um ou mais baldes Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Crie uma varredura para um ou mais baldes Amazon S3

Uma vez adicionados os baldes como fontes de dados do Purview, pode configurar uma varredura para executar em intervalos programados ou imediatamente.

1. Navegue até à área de Fontes de Visão Azure e, em seguida, faça uma das **seguintes:**

    - Na **vista mapa,** selecione Novo ícone de  ![ digitalização.](./media/register-scan-amazon-s3/new-scan-button.png) na sua caixa de origem de dados.
    - Na **vista Lista,** paire sobre a linha para a sua fonte de dados e selecione Novo ícone de  ![ ](./media/register-scan-amazon-s3/new-scan-button.png) digitalização.

1. No **painel Scan...** que se abre à direita, defina os seguintes campos e, em seguida, **selecione Continue:**

    |Campo  |Descrição  |
    |---------|---------|
    |**Nome**     |  Introduza um nome significativo para a sua digitalização ou use o padrão.       |
    |**Tipo** |Apresentado apenas se tiver adicionado a sua conta AWS, com todos os baldes incluídos. <br><br>As opções atuais incluem apenas **All**  >  **Amazon S3**. Mantenha-se atento a mais opções para selecionar à medida que a matriz de suporte do Purview se expande. |
    |**Credencial**     |  Selecione uma credencial de Provisão com o seu papel ARN. <br><br>**Dica**: Se pretender criar uma nova credencial neste momento, selecione **New**. Para obter mais informações, consulte [Criar uma credencial de provisão para a sua verificação do balde AWS](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    | **Amazon S3**    |   Apresentado apenas se tiver adicionado a sua conta AWS, com todos os baldes incluídos. <br><br>Selecione um ou mais baldes para digitalizar ou **selecione todos** para digitalizar todos os baldes da sua conta.      |
    | | |

    A visão verifica automaticamente se a função ARN é válida e que os baldes e objetos dentro dos baldes estão acessíveis e, em seguida, continua se a ligação for bem sucedida.

    > [!TIP]
    > Para introduzir valores diferentes e testar a ligação antes de continuar, selecione **a ligação de teste** na parte inferior direita antes de selecionar **Continue**.
    >

1. No painel **de definição de regras de verificação,** selecione o conjunto de regras padrão **AmazonS3** ou selecione **nova regra de verificação definida** para criar um novo conjunto de regras personalizadas. Uma vez selecionada a regra, selecione **Continue**.

    Se selecionar para criar um novo conjunto de regras de verificação personalizada, utilize o assistente para definir as seguintes definições:

    |Painel  |Description  |
    |---------|---------|
    |**Novo conjunto de regras de digitalização** /<br>**Digitalizar a descrição da regra**    |   Introduza um nome significativo e uma descrição opcional para o seu conjunto de regras      |
    |**Selecione tipos de ficheiros**     | Selecione todos os tipos de ficheiros que pretende incluir na digitalização e, em seguida, selecione **Continue**.<br><br>Para adicionar um novo tipo de ficheiro, selecione **novo tipo de ficheiro** e defina o seguinte: <br>- A extensão do ficheiro que pretende adicionar <br>- Uma descrição opcional  <br>- Se o conteúdo do ficheiro tem um delimiter personalizado ou se é um tipo de ficheiro do sistema. Em seguida, insira o seu delimiter personalizado ou selecione o tipo de ficheiro do sistema. <br><br>Selecione **Criar** para criar o seu tipo de ficheiro personalizado.     |
    |**Selecione regras de classificação**     |   Navegue e selecione as regras de classificação que pretende executar no seu conjunto de dados.      |
    |     |         |

    **Selecione Criar** quando terminar para criar o seu conjunto de regras.

1. No conjunto de um painel de gatilho de **digitalização,** selecione um dos seguintes e, em seguida, selecione **Continue**:

    - **Recorrente** para configurar um horário para uma varredura recorrente
    - **Uma vez** para configurar uma digitalização que começa imediatamente

1. No **painel de verificação,** verifique os seus dados de digitalização para confirmar que estão corretos e, em seguida, selecione **Guardar** ou **Guardar e Executar** se tiver selecionado Uma **vez** no painel anterior.

    > [!NOTE]
    > Uma vez iniciado, a digitalização pode demorar até 24 horas para ser concluída. Poderá rever os seus **Relatórios insight** e pesquisar no catálogo 24 horas depois de iniciar cada digitalização.
    >

Para obter mais informações, consulte [os resultados da digitalização da Purga.](#explore-purview-scanning-results)

## <a name="explore-purview-scanning-results"></a>Explore os resultados da digitalização do Purview

Uma vez que uma varredura de Purview esteja completa nos seus baldes Amazon S3, perfure na área de **Fontes** de Pureza para ver o histórico de digitalização.

Selecione uma fonte de dados para ver os seus dados e, em seguida, selecione o **separador Scans** para visualizar quaisquer verificações atualmente em execução ou concluídas.
Se adicionou uma conta AWS com vários baldes, o histórico de digitalização de cada balde é mostrado na conta.

Por exemplo:

![Mostre os testes de balde AWS S3 sob a sua fonte de conta AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Use as outras áreas de Purview para descobrir detalhes sobre o conteúdo da sua propriedade de dados, incluindo os seus baldes Amazon S3:

- **Procure no catálogo de dados da Purview** e filtre por um balde específico. Por exemplo:

    ![Procure no catálogo os ativos da AWS S3.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Consulte os relatórios insight** para ver estatísticas para a classificação, etiquetas de sensibilidade, tipos de ficheiros e mais detalhes sobre o seu conteúdo.

    Todos os relatórios do Purview Insight incluem os resultados de digitalização do Amazon S3, juntamente com os restantes resultados das suas fontes de dados Azure. Quando relevante, foi adicionado um tipo adicional de ativos **Amazon S3** às opções de filtragem do relatório.

    Para obter mais informações, consulte os [Insights de Compreensão em Azure Purview](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>Permissões mínimas para a sua política AWS

O procedimento predefinido para [criar um papel AWS para o Purview](#create-a-new-aws-role-for-purview) usar ao digitalizar os seus baldes S3 utiliza a política **AmazonS3ReadOnlyAccess.**

A política **AmazonS3ReadOnlyAccess** fornece permissões mínimas necessárias para digitalizar os baldes S3, podendo incluir outras permissões também.

Para aplicar apenas as permissões mínimas necessárias para digitalizar os seus baldes, crie uma nova política com as permissões listadas nas seguintes secções, dependendo se pretende digitalizar um único balde ou todos os baldes da sua conta.

Aplique a sua nova política no papel em vez de **AmazonS3ReadOnlyAccess.**

### <a name="individual-buckets"></a>Baldes individuais

Ao digitalizar baldes S3 individuais, as permissões mínimas AWS incluem:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Certifique-se de que define o seu recurso com o nome específico do balde. Por exemplo:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Todos os baldes na sua conta

Ao digitalizar todos os baldes da sua conta AWS, as permissões mínimas de AWS incluem:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Certifique-se de definir o seu recurso com um wildcard. Por exemplo:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os relatórios Azure Purview Insight:

> [!div class="nextstepaction"]
> [Compreender as Informações no Azure Purview](concept-insights.md)
