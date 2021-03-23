---
title: Aplicar o Ambiente de Trabalho Remoto nos Serviços de Cloud (suporte alargado)
description: Ativar o Ambiente de Trabalho Remoto para Serviços na Nuvem (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 94827916f28c9028d46bf7b5461a4fbd941b2a96
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773407"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Aplicar a extensão de ambiente de trabalho remoto aos Serviços de Nuvem Azure (suporte alargado)

O portal Azure utiliza a extensão remota do ambiente de trabalho para permitir o ambiente de trabalho remoto mesmo depois de a aplicação ser implementada. As definições de ambiente de trabalho remoto para o seu Serviço cloud permitem-lhe ativar o ambiente de trabalho remoto, atualizar a conta de administrador local, selecionar os certificados utilizados na autenticação e definir a data de validade para esses certificados. 

## <a name="apply-remote-desktop--extension"></a>Aplicar extensão de ambiente de trabalho remoto
1. Navegue para o Serviço de Cloud para ativar o ambiente de trabalho remoto e selecione **"Remote Desktop"** no painel de navegação esquerdo.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="A imagem mostra a seleção da opção Remote Desktop no portal Azure":::

2. Selecione **Adicionar**.
3. Escolha as funções para ativar o ambiente de trabalho remoto.
4. Preencha os campos necessários para o nome de utilizador, palavra-passe, caducidade e certificado (não necessário).
> [Nota] A palavra-passe para ambiente de trabalho remoto deve ter entre 8-123 caracteres de comprimento e deve satisfazer pelo menos 3 requisitos de complexidade da palavra-passe a partir do seguinte: 1) Contém um caracteres maiúsculas 2) Contém um caracteres minúsculos 3) Contém um dígito numérico 4) Contém um carácter especial 5) Não são permitidos caracteres de controlo

   :::image type="content" source="media/remote-desktop-2.png" alt-text="A imagem mostra a inserção das informações necessárias para se ligar ao ambiente de trabalho remoto.":::

5. Quando terminar, **selecione Save**. Levará alguns momentos até que as suas instâncias de papel estejam prontas para receber ligações.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Conecte-se a instâncias de função com desktop remoto ativado
Uma vez ativado o ambiente de trabalho remoto nas funções, pode iniciar uma ligação diretamente a partir do portal Azure.

1. Clique em **Funções e Instâncias** para abrir as definições de instância.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="A imagem mostra a seleção das funções e instâncias na lâmina de configuração.":::

2. Selecione uma instância de função que tenha o ambiente de trabalho remoto configurado.
3. Clique **em Connect** para descarregar um ficheiro de ligação ao ambiente de trabalho remoto.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="A imagem mostra a seleção da instância de função do trabalhador no portal Azure.":::
    
4. Abra o ficheiro para ligar à instância de função.


## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
