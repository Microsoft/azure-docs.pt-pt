---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013280"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Definir a encriptação numa conta de armazenamento

1. No portal Azure, insira o nome da conta de armazenamento que pretende encriptar no campo **Procurar** no topo do ecrã.  Os fósforos aparecerão abaixo do campo de pesquisa.
1. Selecione a conta de armazenamento que procura. O ecrã da conta de armazenamento aparecerá.
1. Selecione **encriptação**.
1. Selecione as teclas geridas pela Microsoft ou as teclas geridas pelo Cliente.

### <a name="use-microsoft-managed-keys"></a>Utilize chaves geridas pela Microsoft

Por predefinição, os dados na conta de armazenamento são encriptados utilizando as teclas geridas pela Microsoft.

### <a name="use-customer-managed-keys"></a>Utilizar as chaves geridas pelo cliente

1. Selecione **as teclas geridas pelo Cliente**.
1. Selecione **URI da tecla** ou **selecione a partir do cofre de teclas**.
    1. Se selecionar **Introduzir URI na tecla,** introduza a chave URI no campo Key URI e selecione a subscrição. (Pode já estar selecionado para si.)
    1. Se selecionar **Selecione a partir do cofre de teclas,** selecione **um cofre e uma tecla de teclas**. Aparecerá a tecla Select do ecrã Azure Key Vault.
1. Selecione o **Cofre de Chaves** que pretende utilizar e selecione uma chave que já tem no cofre da chave ou crie uma nova **chave**.
    1. Se optar por criar uma nova chave, **selecione Gerar** ou **Importar** a partir das **Opções** cair. Só pode importar chaves RSA.
    1. Para gerar uma nova chave, dê à chave um nome no campo **Nome** e, em seguida, selecione o tipo chave:
        1. RSA - Tamanhos das Chaves: 2048.3072 ou 4096. Isto é o que a maioria dos clientes escolhe.
        1. EC - Nomes da Curva Elíptica: P-256, P-384, P-521 ou P-256K
        1. Opcionalmente, pode definir as datas de ativação e validade da chave.
        1. Selecione **Sim** para ativar a rotação automática da chave.
        1. Selecione **Criar**.
    1. Para importar uma chave, selecione o ficheiro para carregar clicando em qualquer lugar do **campo de ficheiros Select**.
        1. Dê à chave um nome no campo **Nome.**
        1. Opcionalmente, pode definir as datas de ativação e validade da chave.
        1. Selecione **Sim** para ativar a rotação automática da chave.
        1. Selecione **Criar**.
    1. **Selecione** selecione para selecionar esta chave para encriptar a sua conta de armazenamento. Serão levados de volta para o ecrã de encriptação.
1. **É IMPORTANTE!** **Selecione Guardar** para guardar as definições de encriptação ou tudo o que acabou de fazer será perdido.
