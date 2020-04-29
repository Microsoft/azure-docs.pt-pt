---
title: Separador de test drive de máquina virtual no Portal do Parceiro cloud para o Mercado Azure
description: Descreve o separador Test Drive utilizado na criação de uma oferta De VM do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: adac73d64feb6280c5043776249072e9f7595faa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142976"
---
# <a name="virtual-machine-test-drive-tab"></a>Separador de test drive de máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create a Azure Virtual Machine para](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) gerir as suas ofertas migratórias.

O separador **Test Drive** da página **New Offer** permite-lhe fornecer aos seus potenciais clientes uma demonstração prática e auto-guiada das principais funcionalidades e benefícios do seu produto, demonstrado num cenário padronizado.  Test Drive é uma característica opcional para os tipos de oferta que suportam test drive.  Test Drive requer que os ativos de apoio sejam corretamente implementados.  Para mais informações, consulte o artigo [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Para ativar esta funcionalidade, no separador **Test Drive,** clique na opção **Sim** em **Ativar um Test Drive**.  O separador **Test Drive** apresenta os campos disponíveis para edição.  Um asterisco anexado (*) no nome do campo indica que é necessário.

![Separador Test Drive no formulário New Offer para máquinas virtuais](./media/publishvm_007.png)


## <a name="field-values"></a>Valores de campo

A tabela seguinte descreve o propósito e o conteúdo destes campos.  Os campos necessários são indiciados por um asterisco (*).


|    Campo                  |       Descrição                                                            |
|  ---------                |     ---------------                                                          |
|  *Detalhes*   |  |
| **Descrição\***           | Forneça uma visão geral do seu cenário de Test Drive. Este texto será mostrado ao utilizador enquanto o Test Drive está a ser aprovisionado. Este campo suporta HTML básico se pretender fornecer conteúdo formatado.  |
| **Manual do utilizador\***           | Faça upload de um manual detalhado do utilizador (.pdf) que ajuda os utilizadores do Test Drive a entender como usar a sua solução.  |
| **Vídeo de demonstração de test drive** | Faça upload de um vídeo que mostre a sua solução.  Se escolheu esta opção, deve fornecer um nome, URL para o vídeo (hospedado no YouTube ou Vimeo) e uma miniatura (533x324 pixel) para o vídeo. |
| *Configuração Técnica* |  |
| **de Instâncias\***             | Especifique a disponibilidade da região e a disponibilidade relativa da instância vm (clique no ícone da informação para mais detalhes).  <br/>As sessões de Test Drive simultâneas potenciais não devem exceder o limite de quota para a sua subscrição.  O primeiro é calculado como: [Número de Regiões Selecionadas] x [Casos quentes] + [Número de Regiões Selecionadas] x [Casos quentes] + [Número de Regiões Selecionadas] x [Casos frios] |
| **Duração do test drive\***   | Duração máxima da sessão em horas. A sessão test drive termina automaticamente após este período de tempo ser ultrapassado.  |
|**Modelo de braço de acionamento de teste\***| Faça upload do modelo de Gestor de Recursos Azure associado a esta Unidade de Teste. Para mais informações, consulte o modelo de implementação da [máquina virtual transformadora para test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Informação de acesso\***    | Informação de acesso e login do Gestor de Recursos Azure, escrita como texto simples ou HTML simples. |
| *Detalhes da subscrição de implementação de unidade de teste* |  |
| **ID de Subscrição do Azure\*** | Pode ser obtido através da sessão no [portal microsoft Azure](https://ms.portal.azure.com) e clicando em **Subscrições** na barra de menus esquerda. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Este identificador deve ser um `a83645ac-1234-5ab6-6789-1h234g764ghty`GUID da forma .|
| **Id de inquilino azure AD\***    | Id de inquilino do Diretório Ativo Azure.  Pode ser obtido assinando no portal do [Microsoft Azure](https://ms.portal.azure.com) e clicando no **Azure Ative Directory** na barra de menus esquerda, clicando em **Propriedades** na barra de menu sinuosa e, em seguida, copiando o ID do **Diretório** a partir do formulário.  Este identificador também deve ser um GUID.  Se estiver em branco, então deve criar uma identificação de inquilino para a sua organização. |
| **Azure AD App Id\***       | Identificador para a sua solução Azure VM registada  |
| **Chave da aplicação Azure AD\***      | Chave de autenticação para a sua solução registada |
|   |   |


## <a name="next-steps"></a>Passos seguintes

No próximo separador [Marketplace,](./cpp-marketplace-tab.md) irá fornecer informações de marketing e informações legais sobre a sua solução.
