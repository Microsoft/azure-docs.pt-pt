---
title: Armazenar e usar certificados nos Serviços Cloud do Azure (suporte alargado)
description: Processos de armazenamento e utilização de certificados nos Serviços cloud Azure (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744726"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Utilize certificados com serviços Azure Cloud (suporte alargado)

O Key Vault é utilizado para armazenar certificados associados aos Serviços Cloud (suporte alargado). As Portarias-chave podem ser criadas através do [portal Azure](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) e [da PowerShell.](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell) Adicione os certificados ao Key Vault e, em seguida, faça referência às impressões digitais do certificado no ficheiro de configuração de serviço. Também precisa de ativar o Key Vault para obter permissões apropriadas para que o recurso Cloud Services (suporte alargado) possa obter o certificado armazenado como segredos do Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Faça o upload de um certificado para o Cofre de Chaves 

1.  Inscreva-se no portal Azure e navegue até ao Cofre de Chaves. Se não tiver um Cofre chave configurado, pode optar por criar um nesta mesma janela.

2. Selecione **Access polices**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="A imagem mostra a seleção das políticas de acesso a partir da lâmina do cofre da chave.":::

3. Certifique-se de que as políticas de acesso incluem as seguintes propriedades:
    - **Permitir o acesso a Máquinas Virtuais Azure para implantação**
    - **Permitir o acesso ao Azure Resource Manager para a implementação do modelo** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="A imagem mostra a janela das políticas de acesso no portal Azure.":::
 
4.  Selecione **Certificados** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="A imagem mostra a seleção da opção de certificados a partir da janela das políticas de lâminas do cofre chave no portal Azure.":::

5. Selecione **Gerar / Importar**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="A imagem mostra a seleção da opção de geração/importação":::

4.  Preencha as informações necessárias para terminar o upload do certificado. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="A imagem mostra a janela de importação no portal Azure.":::

5.  Adicione os dados do certificado à sua função no ficheiro Configuração de Serviço (.cscfg). Certifique-se de que a impressão digital do certificado no portal Azure corresponde à impressão digital no ficheiro de Configuração de Serviço (.cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Próximos passos 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).