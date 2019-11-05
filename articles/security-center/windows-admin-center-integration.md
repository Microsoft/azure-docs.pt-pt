---
title: Como integrar o centro de administração do Windows à central de segurança do Azure | Microsoft Docs
description: Este artigo explica como integrar a central de segurança do Azure com o centro de administração do Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 48bf2cc3feb9ea3983200a2ef778669b5cfe36fa
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521595"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integrar a central de segurança do Azure com o centro de administração do Windows

O centro de administração do Windows é uma ferramenta de gerenciamento para seus servidores Windows. É um único local para que os administradores de sistema acessem a maioria das ferramentas de administração mais usadas. No centro de administração do Windows, você pode integrar diretamente seus servidores locais à central de segurança do Azure. Você pode exibir um resumo de suas recomendações de segurança e alertas diretamente na experiência do centro de administração do Windows.

> [!NOTE]
> Sua assinatura do Azure e o espaço de trabalho Log Analytics associado precisam ter a camada Standard da central de segurança habilitada para habilitar a integração do centro de administração do Windows.
> A camada Standard será gratuita pelos primeiros 30 dias se você ainda não o tiver usado na assinatura e no espaço de trabalho. Para obter mais informações, consulte [a página de informações de preços](security-center-pricing.md).
>

Quando você tiver integrado com êxito um servidor do centro de administração do Windows à central de segurança do Azure, poderá:

* Exibir alertas de segurança e recomendações dentro da extensão da central de segurança no centro de administração do Windows
* Exibir a postura de segurança e recuperar informações detalhadas adicionais dos servidores gerenciados do centro de administração do Windows na central de segurança dentro do portal do Azure (ou por meio de uma API)

Ao combinar essas duas ferramentas, a central de segurança se torna seu único painel para exibir todas as suas informações de segurança, qualquer que seja o recurso: proteger o centro de administração do Windows gerenciado por servidores locais, suas VMs e quaisquer cargas de trabalho adicionais de PaaS.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Integração de servidores gerenciados do centro de administração do Windows à central de segurança

1. No centro de administração do Windows, selecione um dos servidores e, no painel **ferramentas** , selecione a extensão da central de segurança do Azure:

    ![Extensão da central de segurança do Azure no centro de administração do Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se o servidor já estiver integrado à central de segurança, a janela de configuração não será exibida.

1. Clique em **entrar no Azure e configurar**.
    ![a integração da extensão do centro de administração do Windows à central de segurança do Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Siga as instruções para conectar o servidor à central de segurança. Depois de inserir os detalhes e a confirmação necessários, a central de segurança fará as alterações de configuração necessárias para garantir que todas as seguintes opções sejam verdadeiras:
    * Um gateway do Azure está registrado.
    * O servidor tem um espaço de trabalho para relatar e uma assinatura associada.
    * A solução de Log Analytics da camada Standard da central de segurança está habilitada no espaço de trabalho. Essa solução fornece os recursos da camada Standard da central de segurança para *todos os* servidores e máquinas virtuais que se reportam a esse espaço de trabalho.
    * O preço da camada Standard da central de segurança para a máquina virtual está habilitado na assinatura.
    * O Microsoft Monitoring Agent (MMA) está instalado no servidor e configurado para relatar para o espaço de trabalho selecionado. Se o servidor já se reportar a outro espaço de trabalho, ele também será configurado para relatar para o espaço de trabalho selecionado recentemente.

    > [!NOTE]
    > Pode levar algum tempo após a integração para que as recomendações apareçam. Na verdade, dependendo da atividade do servidor, você não receberá *nenhum* alerta. Para gerar alertas de teste para testar se os alertas estão funcionando corretamente, siga as instruções no [procedimento de validação de alerta](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Exibindo recomendações e alertas de segurança no centro de administração do Windows

Uma vez integrado, você pode exibir seus alertas e recomendações diretamente na área da central de segurança do Azure do centro de administração do Windows. Clique em uma recomendação ou um alerta para exibi-los no portal do Azure. Lá, você obterá informações adicionais e aprenderá a corrigir problemas.

[![recomendações e alertas da central de segurança como visto no centro de administração do Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Exibindo recomendações de segurança e alertas para servidores gerenciados do centro de administração do Windows na central de segurança
Na central de segurança do Azure:

* Para exibir as recomendações de segurança para todos os seus servidores do centro de administração do Windows, abra **computação & aplicativos** e clique na guia **VMs e computadores** . filtre a lista por recurso "servidor", conforme mostrado aqui:

    [![exibir recomendações de segurança para servidores gerenciados do centro de administração do Windows](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Para exibir alertas de segurança para todos os servidores do centro de administração do Windows, abra **alertas de segurança**. Clique em **Filtrar** e verifique se **somente** "não Azure" está selecionado:

    ![Filtrar alertas de segurança para servidores gerenciados do centro de administração do Windows](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![exibir alertas de segurança para servidores gerenciados do centro de administração do Windows](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)