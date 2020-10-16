---
title: Encriptação em Azure Data Lake Storage Gen1 Microsoft Docs
description: A encriptação no Azure Data Lake Storage Gen1 ajuda-o a proteger os seus dados, a implementar políticas de segurança da empresa e a cumprir os requisitos de conformidade regulamentar. Este artigo apresenta uma descrição geral da estrutura e descreve alguns dos aspetos técnicos da implementação.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: f924cb7462f7f8c9939ec261b7ef200ceb8ea70b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109158"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Encriptação de dados em Azure Data Lake Storage Gen1

A encriptação no Azure Data Lake Storage Gen1 ajuda-o a proteger os seus dados, a implementar políticas de segurança da empresa e a cumprir os requisitos de conformidade regulamentar. Este artigo apresenta uma descrição geral da estrutura e descreve alguns dos aspetos técnicos da implementação.

Data Lake Storage Gen1 suporta encriptação de dados tanto em repouso como em trânsito. Para os dados em repouso, a Data Lake Storage Gen1 suporta "on by default", encriptação transparente. Eis o que cada um destes termos significa em maior detalhe:

* **Por predefinição**: Quando cria uma nova conta Desaquim de Armazenamento de Dados Gen1, a definição padrão permite encriptação. Posteriormente, os dados que são armazenados na Data Lake Storage Gen1 são sempre encriptados antes de armazenar em meios persistentes. Este é o comportamento para todos os dados e não pode ser alterado depois de uma conta ser criada.
* **Transparente**: Data Lake Storage Gen1 encripta automaticamente os dados antes de persistir e desencripta dados antes da recuperação. A encriptação é configurada e gerida ao nível da conta Desequim de Armazenamento de Dados Gen1 por um administrador. Não são feitas alterações às APIs de acesso aos dados. Assim, não são necessárias alterações em aplicações e serviços que interajam com a Data Lake Storage Gen1 por causa da encriptação.

Os dados em trânsito (também conhecidos como dados em movimento) também são sempre encriptados na Data Lake Storage Gen1. Para além de encriptar os dados antes de serem armazenados em suportes de dados persistentes, os dados também são sempre protegidos em trânsito com HTTPS. HTTPS é o único protocolo suportado para as interfaces de armazenamento de dados Gen1 REST. O seguinte diagrama mostra como os dados ficam encriptados na Data Lake Storage Gen1:

![Diagrama de encriptação de dados na Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Configurar encriptação com data lake storage gen1

A encriptação para data lake storage gen1 é configurada durante a criação de conta, e é sempre ativada por padrão. Pode gerir as chaves por si mesmo, ou permitir que a Data Lake Storage Gen1 as gere (este é o padrão).

Para obter mais informações, veja o artigo [Introdução](./data-lake-store-get-started-portal.md).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Como a encriptação funciona na Data Lake Storage Gen1

As seguintes informações abrangem como gerir as chaves de encriptação principais, e explica os três tipos diferentes de chaves que pode usar na encriptação de dados para data lake storage gen1.

### <a name="master-encryption-keys"></a>Chaves de encriptação mestras

Data Lake Storage Gen1 fornece dois modos para a gestão das chaves de encriptação principal (MEKs). Por agora, vamos supor que a chave de encriptação mestra é a chave de nível superior. O acesso à chave de encriptação principal é necessário para desencriptar quaisquer dados armazenados na Data Lake Storage Gen1.

Os dois modos para gerir a chave de encriptação mestra são os seguintes:

*   Chaves geridas por serviços
*   Chaves geridas pelo cliente

Em ambos os modos, a chave de encriptação mestra é protegida mediante armazenamento no Azure Key Vault. O Key Vault é um serviço totalmente gerido e altamente seguro do Azure, que pode ser utilizado para salvaguardar chaves criptográficas. Para obter mais informações, veja [Key Vault](https://azure.microsoft.com/services/key-vault).

Segue-se uma breve comparação das capacidades proporcionadas pelos dois modos de gestão de MEKs.

| Pergunta | Chaves geridas por serviços | Chaves geridas pelo cliente |
| -------- | -------------------- | --------------------- |
|Como são armazenados os dados?|São sempre encriptados antes de serem armazenados.|São sempre encriptados antes de serem armazenados.|
|Onde é armazenada a Chave de Encriptação Mestra?|Cofre de Chaves|Cofre de Chaves|
|As chaves de encriptação são armazenadas de forma desprotegida, fora do Key Vault? |Não|Não|
|É possível obter a MEK a partir do Key Vault?|Não. Depois de a MEK ser armazenada no Key Vault, só pode ser utilizada para encriptação e desencriptação.|Não. Depois de a MEK ser armazenada no Key Vault, só pode ser utilizada para encriptação e desencriptação.|
|Quem é o proprietário da instância do Key Vault e da MEK?|O serviço de armazenamento de dados Lake Gen1|O utilizador é o proprietário da instância do Key Vault, que pertence à sua própria subscrição do Azure. A MEK no Key Vault pode ser gerida por software ou hardware.|
|Pode revogar o acesso ao MEK para o serviço Desarrumação gen1 do Data Lake?|Não|Yes. Pode gerir as listas de controlo de acesso no Key Vault e remover as entradas de controlo de acesso à identidade do serviço para o serviço Data Lake Storage Gen1.|
|Pode eliminar permanentemente a MEK?|Não|Yes. Se eliminar o MEK do Key Vault, os dados da conta Descodificar a Gen1 de Armazenamento de Dados não podem ser desencriptados por ninguém, incluindo o serviço Desemboado Desembose de Armazenamento de Dados Gen1. <br><br> Se tiver criado explicitamente uma cópia de segurança da MEK antes de a eliminar do Key Vault, a MEK pode ser restaurada e os dados recuperados. No entanto, se não tiver feito o back up do MEK antes de o eliminar do Key Vault, os dados na conta de Data Lake Storage Gen1 nunca poderão ser desencriptados posteriormente.|


Para além desta diferença, ou seja, de quem gere a MEK e a instância do Key Vault onde esta reside, o resto do design é igual em ambos os modos.

É importante não esquecer o seguinte quando escolher o modo para as chaves de encriptação mestras:

*   Pode escolher se utiliza chaves geridas pelo cliente ou chaves geridas pelo serviço quando forte uma conta Gen1 de Armazenamento de Data Lake.
*   Depois de uma conta Gen1 de armazenamento de datas ser a forada, o modo não pode ser alterado.

### <a name="encryption-and-decryption-of-data"></a>Encriptação e desencriptação de dados

São utilizados três tipos de chaves no design da encriptação de dados. A tabela seguinte fornece um resumo:

| Chave                   | Abreviatura | Associada a | Localização do armazenamento                             | Tipo       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de Encriptação Mestra | MEK          | Uma conta Gen1 de armazenamento de data lake | Cofre de Chaves                              | Assimétrica | Pode ser gerido pela Data Lake Storage Gen1 ou por si.                                                              |
| Chave de Encriptação de Dados   | DEK          | Uma conta Gen1 de armazenamento de data lake | Armazenamento persistente, gerido pelo serviço Data Lake Storage Gen1 | Simétrica  | O DEK é encriptado pela MEK. O DEK encriptado é o que é armazenado no suporte de dados persistente. |
| Chave de Encriptação de Blocos  | BEK          | Um bloco de dados | Nenhum                                         | Simétrica  | A BEK é obtida a partir da DEK e do bloco de dados.                                                      |

O diagrama seguinte ilustra estes conceitos:

![Chaves na encriptação de dados](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo quando um ficheiro vai ser desencriptado:
1.  Verifique se o DEK para a conta DeK para o Armazenamento de Dados Gen1 está em cache e pronto para ser utilizado.
    - Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória. Está agora pronta para ser utilizada.
2.  Relativamente a cada bloco de dados no ficheiro:
    - Leia o bloco de dados encriptado no armazenamento persistente.
    - Gere a BEK a partir da DEK e do bloco de dados encriptado.
    - Utilize a BEK para desencriptar os dados.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo quando vai ser encriptado um bloco de dados:
1.  Verifique se o DEK para a conta DeK para o Armazenamento de Dados Gen1 está em cache e pronto para ser utilizado.
    - Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória. Está agora pronta para ser utilizada.
2.  Gere uma BEK exclusivo para o bloco de dados a partir da DEK.
3.  Utilize a encriptação AES-256 para encriptar o bloco de dados com a BEK.
4.  Armazene o bloco de dados encriptado no armazenamento persistente.

> [!NOTE] 
> O DEK é sempre armazenado de forma encriptada pelo MEK, no suporte de dados persistente ou colocado em cache na memória.

## <a name="key-rotation"></a>Rotação de chaves

Quando estiver a utilizar chaves geridas pelo cliente, pode rodar a MEK. Para aprender a configurar uma conta Desagérido de Armazenamento de Dados Gen1 com chaves geridas pelo cliente, consulte [Começar](./data-lake-store-get-started-portal.md).

### <a name="prerequisites"></a>Pré-requisitos

Ao configurar a conta Desagérisy De armazenamento de Dados, optou por usar as suas próprias chaves. Esta opção não pode ser alterada depois de a conta ter sido criada Os passos seguintes partem do princípio de que está a utilizar chaves geridas pelo cliente (ou seja, que escolheu as suas próprias chaves a partir do Key Vault).

Note que se utilizar as opções padrão para encriptação, os seus dados são sempre encriptados utilizando teclas geridas pela Data Lake Storage Gen1. Nesta opção, você não tem a capacidade de rodar chaves, uma vez que são geridas pela Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Como rodar o MEK em Data Lake Storage Gen1

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue na instância Key Vault que armazena as suas chaves associadas à sua conta Desembos De Armazenamento de Dados Gen1. Selecione **Chaves**.

    ![Captura de ecrã do Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Selecione a chave associada à sua conta Desarrumação Gen1 e crie uma nova versão desta chave. Note que data lake storage Gen1 atualmente apenas suporta a rotação chave para uma nova versão de uma chave. Não suporta a rotação para uma chave diferente.

   ![Captura de ecrã da janela Chaves, com a opção Nova Versão realçada](./media/data-lake-store-encryption/keynewversion.png)

4. Navegue na conta Gen1 de armazenamento de dados e selecione **Encriptação**.

   ![Screenshot da janela da conta Gen1 de armazenamento de dados, com encriptação em destaque](./media/data-lake-store-encryption/select-encryption.png)

5. Verá uma mensagem a informar de que está disponível uma versão nova da chave. Clique em **Rodar Chave** para atualizar a chave para a versão nova.

   ![Screenshot da janela Gen1 de armazenamento de data lake com mensagem e chave rotativa em destaque](./media/data-lake-store-encryption/rotatekey.png)

Esta operação deve demorar menos de dois minutos e não se prevê qualquer período de indisponibilidade durante a rotação de chaves. Depois de a operação estar concluída, a versão nova da chave estará em uso.

> [!IMPORTANT]
> Depois de concluída a operação de rotação da chave, a versão antiga da chave já não é utilizada ativamente para encriptar os dados.  No entanto, em situações raras de falha inesperada em que até as cópias redundantes dos seus dados são afetadas, os dados podem ser restaurados a partir de uma cópia de segurança que ainda está a utilizar a chave antiga. Para garantir que os dados estão acessíveis nestas circunstâncias raras, mantenha uma cópia da versão anterior da sua chave de encriptação. Consulte [a orientação de recuperação de desastres para obter dados na Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) para obter as melhores práticas para o seu planeamento de recuperação de desastres.