---
title: Criar uma solução de análise de dados pré-configurados com o Azure e o Azure Stack | Documentos da Microsoft
description: Saiba como criar uma solução de análise de dados pré-configurados com o Azure e o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 0261dff10e553ecbd39d394eb19e7ad3dd76f0a2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340971"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Tutorial: Criar uma solução de análise de dados pré-configurados com o Azure e o Azure Stack 

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como utilizar aplicações no local e ambientes de cloud pública para satisfazer as necessidades das empresas de várias instalações. O Azure Stack oferece uma solução rápida, segura e flexível para recolher, processamento, armazenamento e distribuição de dados locais e remotos, especialmente quando a segurança, da confidencialidade, política da empresa e os requisitos de regulamentação podem ser diferentes entre localizações e os utilizadores.

Neste padrão, os seus clientes estão a recolher dados que requer a análise no ponto de coleção para que podem ser feitas a tomar decisões rápidas. Muitas vezes nesta recolha de dados ocorre sem acesso à Internet. Quando a conectividade for estabelecida, terá de fazer uma análise com muitos recursos de dados para obter informações adicionais. Ainda pode analisar os dados quando uma nuvem pública está muito atrasado ou indisponível.

Neste tutorial, crie um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie o blob de armazenamento de dados não processados.
> - Crie uma nova função de pilha do Azure para mover apagar dados do Azure Stack para o Azure.
> - Crie uma função de acionada pelo armazenamento de Blobs.
> - Crie uma conta de armazenamento do Azure Stack que contém um blob e uma fila.
> - Crie uma função acionada por fila.
> - Função acionada por fila de teste.

> [!Tip]  
> ![pillars.png híbrida](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack é uma extensão do Azure. O Azure Stack coloca a agilidade e inovação da cloud de informática para o seu ambiente no local e ativar a única cloud híbrida que permite-lhe criar e implementar aplicações de híbridas em qualquer lugar.  
> 
> O White Paper [considerações de Design para aplicações híbridas](https://aka.ms/hybrid-cloud-applications-pillars) analisa pilares de qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para estruturar, implementar e utilizar aplicações híbridas. As considerações de design ajudam a otimizar o design de aplicações híbridas, minimizando desafios em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

Uma preparação é necessário para criar esta solução:

-   Uma pilha de Azure instalada e a funcionar (podem encontrar mais informações aqui: [Descrição geral do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   Uma subscrição do Azure. (Criar um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Transferir e instalar o [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

-   Terá de fornecer seus próprios dados a serem processados pelas funções. Dados tem de ser gerado e está disponível para carregar para o contentor de BLOBs de armazenamento do Azure Stack.

## <a name="issues-and-considerations"></a>Problemas e considerações

### <a name="scalability-considerations"></a>Considerações de escalabilidade

As funções do Azure e soluções de armazenamento dimensionam para satisfazer o volume de dados e as exigências de processamento. Para informações de escalabilidade do Azure e destinos, consulte [documentação de escalabilidade do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Considerações de disponibilidade

O armazenamento está a consideração de disponibilidade primária para esse padrão. É necessária para processamento de volume de dados grandes e distribuição de ligação através de ligações rápidas.

### <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Considere como as suas ferramentas de desenvolvimento e controle de origem irão permitir-lhe gerir a sua solução.

## <a name="create-the-raw-data-storage-blob"></a>Criar o blob de armazenamento de dados não processados

O contentor de BLOBs e de conta de armazenamento irá conter todos os dados originais gerados por atividades no local, incluindo atividade de máquina e funcionários, os dados de instalação, métricas de produção e outros relatórios.

1.  Inicie sessão para o [ *portal do Azure Stack*](https://portal.local.azurestack.external/).

2.  No portal do Azure Stack, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **todos os serviços**. Desloque para baixo até **armazenamento** e escolha **contas de armazenamento**. Na janela de contas de armazenamento, escolha **adicionar**.

3.  Utilize as seguintes informações para a conta:

    a.  Nome: **A sua escolha**

    b.  Modelo de implementação: **Resource Manager**

    c.  Tipo de conta: **Armazenamento (para fins gerais V1)**

    d.  Localização: **E.U.A. Oeste**

    e.  Replicação: **Armazenamento localmente redundante (LRS)**

    f.  Desempenho: **Standard**

    g.  Transferência segura necessária: **Desativado**

    h.  Subscrição: Escolher uma

    i.  Grupo de recursos: Especifique um novo grupo de recursos ou selecione um grupo de recursos existente

    j.  Configure redes virtuais: **Desativado**

4.  Selecione **criar para criar a conta de armazenamento**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Depois de criado, selecione o nome da conta de armazenamento.

6.  No painel da conta, sob o **serviço BLOB** cabeçalho, selecione **contentores**.

7.  Na parte superior do painel, selecione **+ contentor.** e selecione **contentor**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Nome: **À sua escolha**

9.  Nível de acesso público: **Contentor** (acesso de leitura anónimo para contentores e blobs)

10.  Selecione **OK**.

## <a name="create-an-azure-stack-function"></a>Criar uma função do Azure Stack

Crie uma nova função do Azure Stack para mover apagar dados do Azure Stack para o Azure.

### <a name="create-the-azure-stack-function-app"></a>Criar a aplicação de funções do Azure Stack

1. Inicie sessão para o [portal do Azure Stack](https://portal.local.azurestack.external).
2. Selecione **Todos os serviços**.
3. Selecione **aplicações Function App** no **Web + móvel** grupo.

4.  Crie a aplicação de função utilizando as definições especificadas na tabela abaixo da imagem.

    | Definição | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | Nome da aplicação | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Carateres válidos são `a` - `z`, `0``-9`, e `-`. |
    | Subscrição | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **Grupo de Recursos** |  |  |
    | myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |  |
    | SO | Windows | O alojamento sem servidor está atualmente apenas disponível ao executar no Windows. |
    | **Plano de alojamento** |  |  |
    | Plano de consumo | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. A predefinição de plano de consumo, os recursos são adicionados dinamicamente conforme exigido pelas suas funções. Neste de alojamento sem servidor, paga apenas o tempo de que execução das funções. |  |
    | Localização | Região mais próximo de | Escolha uma região perto de ou outros serviços com o acesso de funções. |
    | **Conta de armazenamento** |  |  |
    | \<conta de armazenamento criada acima > | O nome da conta de armazenamento nova utilizada pela sua aplicação Function App. Nomes de conta de armazenamento tem de ter entre 3 e 24 carateres de comprimento. O nome só pode utilizar números e letras minúsculas. Também pode utilizar uma conta já existente. |  |

    **Exemplo:**

    ![Definir novas definições da aplicação de funções](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Selecione **Criar** para aprovisionar e implementar a aplicação de função.

6.  Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Selecione **Ir para recurso** para ver a aplicação function app nova.

![Aplicação Function App criada com êxito.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Adicionar uma função para a aplicação de funções do Azure Stack

1.  Criar uma nova função ao clicar em **funções**, em seguida, o **+ nova função** botão.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Selecione **acionador de temporizador**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Selecione **C\#**  como o idioma e o nome da função: `upload-to-azure`  Defina a agenda `0 0 * * * *`, que em CRON notação uma vez é uma hora.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Criar uma função acionada pelo Armazenamento de blobs

1.  Expanda a aplicação de funções e selecione o **+** junto a **funções**.

2.  No campo de pesquisa, escreva `blob` e, em seguida, escolha o idioma pretendido para o **acionador de Blob** modelo.

  ![Escolha o modelo de acionador do armazenamento de Blobs.](media/azure-stack-solution-staged-data-analytics/image10.png)

3.  Utilize as definições especificadas na tabela abaixo:

    | Definição | Valor sugerido | Descrição |
    | ------- | ------- | ------- |
    | Name | Exclusivo na aplicação Function App | Nome desta função acionada por blob. |
    | Caminho | \<caminho da localização de armazenamento acima > | Localização no Armazenamento de blobs a ser monitorizado. O nome de ficheiro do blob é transmitido no enlace como o parâmetro de nome. |
    | Ligação da conta de armazenamento | Ligação de aplicação de função | Pode utilizar a ligação de conta de armazenamento já a ser utilizada pela sua aplicação de função ou crie um novo. |

    **Exemplo:**

    ![Crie a função acionada pelo Armazenamento de blobs.](media/azure-stack-solution-staged-data-analytics/image11.png)

4.  Selecione **criar** para criar a função.

### <a name="test-the-function"></a>Testar a função

1.  No portal do Azure, navegue para a função. Expanda a **registos** na parte inferior da página e certifique-se de que a transmissão de registos não está em pausa.

2.  Abra o Explorador de armazenamento e ligue-se para a conta de armazenamento que criou no início desta secção.

3.  Expanda a conta de armazenamento **contentores de BLOBs**, e os BLOBs que criou anteriormente. Selecione **carregue** e, em seguida **carregar ficheiros**.

    ![Carregue um ficheiro para o contentor de blobs.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  Na caixa de diálogo carregar ficheiros, selecione o campo de ficheiros. Procure um ficheiro num computador local, como um ficheiro de imagem, selecione-o e selecione **aberto** e, em seguida **carregar**.

5.  Volte para os registos de função e certifique-se de que o blob foi lido.

    **Exemplo:**

    ![Ver mensagem nos registos.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Criar uma conta de armazenamento do Azure Stack

Crie uma conta de armazenamento do Azure Stack que contém um blob e uma fila.

### <a name="storage-blob--data-archiving"></a>Arquivamento de dados do Blob de armazenamento

Esta conta de armazenamento irá guardar dois contentores. Estes contentores são utilizados para armazenar dados de arquivo de um blob e uma fila utilizado para o processamento de dados atribuídos para distribuição de escritório principal.

Utilize os passos e as definições descritas acima para criar outra conta e blob contentor de armazenamento como nosso armazenamento de arquivo.

### <a name="storage-queue--filtered-data-holding"></a>Retenção de dados filtrados de fila de armazenamento

1.  Utilize os passos e as definições descritas acima para aceder à conta de armazenamento nova.

2.  Na secção de descrição geral da conta de armazenamento, selecione **fila.**

3.  Selecione o **+ fila** e fill-in **nome** campo e fill-in um nome para a nova fila.

4.  Selecione **OK.**

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Criar uma função acionada por fila

1.  Utilize os passos na secção acima para a criação de função para criar uma função do Azure Stack adicional com um acionador de fila, em vez de um acionador de blob.

2.  Utilize as definições especificadas na tabela abaixo:

    | Definição | Valor sugerido | Descrição |
    | ------- | ------- | ------- |
    | Name | Exclusivo na aplicação Function App | O nome desta função acionada por fila. |
    | Caminho | \<caminho da localização de armazenamento acima > | Localização no armazenamento a ser monitorizado. O nome do ficheiro da fila é transmitido no enlace como o parâmetro de nome. |
    | Ligação da conta de armazenamento | Ligação de aplicação de função | Pode utilizar a ligação de conta de armazenamento já a ser utilizada pela sua aplicação de função ou crie um novo. |

3.  Selecione **criar** para criar a função.

## <a name="test-the-queue-triggered-function"></a>Função acionada por fila de teste

1.  No portal do Azure Stack, navegue para a função. Expanda a **registos** na parte inferior da página e certifique-se de que a transmissão de registos não está em pausa.

2.  Abra o Explorador de armazenamento e ligue-se para a conta de armazenamento que criou no início desta secção.

3.  Expanda a conta de armazenamento **contentores de BLOBs**, e os BLOBs que criou anteriormente. Selecione **carregue** e, em seguida, **carregar ficheiros.**

    ![Carregue um ficheiro para o contentor de blobs.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  Na caixa de diálogo carregar ficheiros, selecione o campo de ficheiros. Procure um ficheiro num computador local, como um ficheiro de imagem, selecione-o e selecione **aberto** e, em seguida **carregar**.

5.  Volte para os registos de função e certifique-se de que o blob foi lido.

  **Exemplo:**

    ![Ver mensagem nos registos.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Dados em conformidade com segurança armazenados e acedidos

Dados da empresa global é em segurança armazenados, processados, distribuídos e acedidos através do Azure e a análise de dados pré-configurados do Azure Stack e diretivas de ponto final personalizado. Atividades de funcionários e o mecanismo de escritório remoto, os dados de instalação e métricas de negócio são continuamente compiladas, armazenadas, testadas de conformidade e distribuídas, de acordo com a política da empresa e de regulamentação regional.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre os padrões de Cloud do Azure, veja [padrões de conceção de Cloud](https://docs.microsoft.com/azure/architecture/patterns).