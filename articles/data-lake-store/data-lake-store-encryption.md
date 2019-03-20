---
title: Encriptação no Azure Data Lake Storage Gen1 | Documentos da Microsoft
description: Encriptação no Azure Data Lake Storage Gen1 ajuda-o a proteger os seus dados, implementar políticas de segurança da empresa e cumprir os requisitos de conformidade a normas. Este artigo apresenta uma descrição geral da estrutura e descreve alguns dos aspetos técnicos da implementação.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098139"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Encriptação de dados no Azure Data Lake Storage Gen1

Encriptação no Azure Data Lake Storage Gen1 ajuda-o a proteger os seus dados, implementar políticas de segurança da empresa e cumprir os requisitos de conformidade a normas. Este artigo apresenta uma descrição geral da estrutura e descreve alguns dos aspetos técnicos da implementação.

Geração 1 de armazenamento do Data Lake suporta a encriptação de dados inativos e em trânsito. Para dados inativos, oferece suporte a geração 1 de armazenamento do Data Lake "por predefinição," encriptação transparente. Eis o que cada um destes termos significa em maior detalhe:

* **Por predefinição**: Quando cria uma nova conta de geração 1 de armazenamento do Data Lake, a predefinição ativa a encriptação. Por esse motivo, os dados armazenados no Data Lake Storage Gen1 são sempre encriptados antes de serem armazenados em suportes de dados persistentes. Este é o comportamento para todos os dados e não pode ser alterado depois de uma conta ser criada.
* **Transparente**: Geração 1 de armazenamento do Data Lake automaticamente encripta os dados antes de persistir e desencripta-os antes da obtenção. A encriptação é configurada e gerida ao nível da conta de geração 1 de armazenamento do Data Lake por um administrador. Não são feitas alterações às APIs de acesso aos dados. Portanto, sem alterações são necessárias em aplicações e serviços que interagem com o Data Lake Storage Gen1 devido à encriptação.

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Storage Gen1. Para além de encriptar os dados antes de serem armazenados em suportes de dados persistentes, os dados também são sempre protegidos em trânsito com HTTPS. O HTTPS é o único protocolo que é suportado para as interfaces REST do Data Lake Storage geração 1. O diagrama seguinte mostra como os dados se tornam encriptados no Data Lake Storage Gen1:

![Diagrama de encriptação de dados no Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Configurar a encriptação com geração 1 de armazenamento do Data Lake

Encriptação para a geração 1 de armazenamento do Data Lake é configurada durante a criação da conta e está sempre ativada por predefinição. Pode gerir as chaves ou permitir que a geração 1 de armazenamento do Data Lake para geri-los por si (esta é a predefinição).

Para obter mais informações, veja o artigo [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Como funciona a encriptação no Data Lake Storage Gen1

As informações seguintes explicam como gerir chaves de encriptação mestras e explica os três tipos diferentes de chaves que pode utilizar na encriptação de dados para a geração 1 de armazenamento do Data Lake.

### <a name="master-encryption-keys"></a>Chaves de encriptação mestras

Geração 1 de armazenamento do Data Lake fornece dois modos para a gestão de chaves de encriptação mestras (MEKs). Por agora, vamos supor que a chave de encriptação mestra é a chave de nível superior. Acesso à chave de encriptação mestra é necessário para desencriptar os dados armazenados no Data Lake Storage Gen1.

Os dois modos para gerir a chave de encriptação mestra são os seguintes:

*   Chaves geridas por serviços
*   Chaves geridas pelo cliente

Em ambos os modos, a chave de encriptação mestra é protegida mediante armazenamento no Azure Key Vault. O Key Vault é um serviço totalmente gerido e altamente seguro do Azure, que pode ser utilizado para salvaguardar chaves criptográficas. Para obter mais informações, veja [Key Vault](https://azure.microsoft.com/services/key-vault).

Segue-se uma breve comparação das capacidades proporcionadas pelos dois modos de gestão de MEKs.

|  | Chaves geridas por serviços | Chaves geridas pelo cliente |
| --- | --- | --- |
|Como são armazenados os dados?|São sempre encriptados antes de serem armazenados.|São sempre encriptados antes de serem armazenados.|
|Onde é armazenada a Chave de Encriptação Mestra?|Cofre de Chaves|Cofre de Chaves|
|As chaves de encriptação são armazenadas de forma desprotegida, fora do Key Vault? |Não|Não|
|É possível obter a MEK a partir do Key Vault?|Não. Depois de a MEK ser armazenada no Key Vault, só pode ser utilizada para encriptação e desencriptação.|Não. Depois de a MEK ser armazenada no Key Vault, só pode ser utilizada para encriptação e desencriptação.|
|Quem é o proprietário da instância do Key Vault e da MEK?|O serviço de geração 1 de armazenamento do Data Lake|O utilizador é o proprietário da instância do Key Vault, que pertence à sua própria subscrição do Azure. A MEK no Key Vault pode ser gerida por software ou hardware.|
|Pode revogar o acesso à MEK do serviço de geração 1 de armazenamento do Data Lake?|Não|Sim. Pode gerir listas de controlo de acesso no Key Vault e remover entradas de controlo de acesso para a identidade de serviço para o serviço de geração 1 de armazenamento do Data Lake.|
|Pode eliminar permanentemente a MEK?|Não|Sim. Se eliminar a MEK do Key Vault, os dados na conta de geração 1 de armazenamento do Data Lake não não possível desencriptar por qualquer pessoa, incluindo o serviço de geração 1 de armazenamento do Data Lake. <br><br> Se tiver criado explicitamente uma cópia de segurança da MEK antes de a eliminar do Key Vault, a MEK pode ser restaurada e os dados recuperados. No entanto, se não efetuou uma cópia de segurança da MEK antes de eliminar do Key Vault, os dados na conta de geração 1 de armazenamento do Data Lake podem nunca possível desencriptar posteriormente.|


Para além desta diferença, ou seja, de quem gere a MEK e a instância do Key Vault onde esta reside, o resto do design é igual em ambos os modos.

É importante não esquecer o seguinte quando escolher o modo para as chaves de encriptação mestras:

*   Pode escolher se utilizar o cliente gerido chaves ou chaves geridas pelo serviço quando Aprovisiona uma conta de geração 1 de armazenamento do Data Lake.
*   Depois de é aprovisionada uma conta do Data Lake Storage Gen1, não é possível alterar o modo.

### <a name="encryption-and-decryption-of-data"></a>Encriptação e desencriptação de dados

São utilizados três tipos de chaves no design da encriptação de dados. A tabela seguinte fornece um resumo:

| Chave                   | Abreviatura | Associada a | Localização do armazenamento                             | Tipo       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de Encriptação Mestra | MEK          | Uma conta de geração 1 de armazenamento do Data Lake | Cofre de Chaves                              | Assimétrica | Pode ser gerida pelo Data Lake Storage Gen1, ou.                                                              |
| Chave de Encriptação de Dados   | DEK          | Uma conta de geração 1 de armazenamento do Data Lake | Armazenamento persistente, gerido pelo serviço de geração 1 de armazenamento do Data Lake | Simétrica  | O DEK é encriptado pela MEK. O DEK encriptado é o que é armazenado no suporte de dados persistente. |
| Chave de Encriptação de Blocos  | BEK          | Um bloco de dados | Nenhuma                                         | Simétrica  | A BEK é obtida a partir da DEK e do bloco de dados.                                                      |

O diagrama seguinte ilustra estes conceitos:

![Chaves na encriptação de dados](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo quando um ficheiro vai ser desencriptado:
1.  Verifique se a DEK da conta do Data Lake Storage Gen1 está em cache e pronta para uso.
    - Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória. Está agora pronta para ser utilizada.
2.  Relativamente a cada bloco de dados no ficheiro:
    - Leia o bloco de dados encriptado no armazenamento persistente.
    - Gere a BEK a partir da DEK e do bloco de dados encriptado.
    - Utilize a BEK para desencriptar os dados.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo quando vai ser encriptado um bloco de dados:
1.  Verifique se a DEK da conta do Data Lake Storage Gen1 está em cache e pronta para uso.
    - Se não for esse o caso, leia a DEK encriptada a partir do armazenamento persistente e envie-a para o Key Vault, para ser desencriptada. Coloque a DEK desencriptada em cache na memória. Está agora pronta para ser utilizada.
2.  Gere uma BEK exclusivo para o bloco de dados a partir da DEK.
3.  Utilize a encriptação AES-256 para encriptar o bloco de dados com a BEK.
4.  Armazene o bloco de dados encriptado no armazenamento persistente.

> [!NOTE] 
> O DEK é sempre armazenado de forma encriptada pelo MEK, no suporte de dados persistente ou colocado em cache na memória.

## <a name="key-rotation"></a>Rotação de chaves

Quando estiver a utilizar chaves geridas pelo cliente, pode rodar a MEK. Para saber como configurar uma conta de geração 1 de armazenamento do Data Lake com chaves geridas pelo cliente, veja [introdução ao](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Pré-requisitos

Quando configurou a conta de geração 1 de armazenamento do Data Lake, tiver optado por utilizar as suas próprias chaves. Esta opção não pode ser alterada depois de a conta ter sido criada Os passos seguintes partem do princípio de que está a utilizar chaves geridas pelo cliente (ou seja, que escolheu as suas próprias chaves a partir do Key Vault).

Tenha em atenção que se usar as opções predefinidas para a encriptação, os dados são sempre encriptados utilizando chaves geridas pelo Data Lake Storage Gen1. Esta opção, não tem a capacidade de Rodar chaves, à medida que são geridos pelo Gen1 de armazenamento do Data Lake.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Como rodar a MEK no Data Lake Storage Gen1

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para a instância do Key Vault que armazena as chaves associadas à sua conta de geração 1 de armazenamento do Data Lake. Selecione **Chaves**.

    ![Captura de ecrã do Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Selecione a chave associada à sua conta do Data Lake Storage Gen1 e criar uma nova versão desta chave. Tenha em atenção que Gen1 de armazenamento do Data Lake atualmente só suporta a rotação de chaves para uma nova versão de uma chave. Não suporta a rotação para uma chave diferente.

   ![Captura de ecrã da janela Chaves, com a opção Nova Versão realçada](./media/data-lake-store-encryption/keynewversion.png)

4. Navegue para a conta de geração 1 do Data Lake Storage e selecione **Encryption**.

   ![Janela de conta de captura de ecrã do Data Lake Storage Gen1, com a encriptação realçada](./media/data-lake-store-encryption/select-encryption.png)

5. Verá uma mensagem a informar de que está disponível uma versão nova da chave. Clique em **Rodar Chave** para atualizar a chave para a versão nova.

   ![Janela de captura de ecrã do Data Lake Storage geração 1 com mensagem e rodar chave realçadas](./media/data-lake-store-encryption/rotatekey.png)

Esta operação deve demorar menos de dois minutos e não se prevê qualquer período de indisponibilidade durante a rotação de chaves. Depois de a operação estar concluída, a versão nova da chave estará em uso.

> [!IMPORTANT]
> Depois de concluída a operação de rotação da chave, a versão antiga da chave já não é utilizada ativamente para encriptar os dados.  No entanto, em situações raras de falha inesperada em que até as cópias redundantes dos seus dados são afetadas, os dados podem ser restaurados a partir de uma cópia de segurança que ainda está a utilizar a chave antiga. Para garantir que os dados estão acessíveis nestas circunstâncias raras, mantenha uma cópia da versão anterior da sua chave de encriptação. Ver [orientações sobre a recuperação após desastre para dados no Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) para melhores práticas para seus planos de recuperação de desastres. 