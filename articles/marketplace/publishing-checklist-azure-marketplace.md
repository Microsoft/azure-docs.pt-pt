---
title: Lista de verificação de publicação do Azure Marketplace Azure
description: Publicar lista de verificação para o Azure Marketplace utilizando o Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: dsindona
ms.openlocfilehash: 0409933a3cd4bb535427cf61bd06207d53d5a96e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387721"
---
# <a name="publishing-checklist-for-azure-marketplace"></a>Publicar Lista de Verificação para o Azure Marketplace    
Reveja os componentes necessários antes de iniciar o processo de publicação.  

Os seguintes artefactos são necessários para completar o fluxo de trabalho de publicação Create Offer no Portal do Parceiro cloud.  

## <a name="checklist"></a>Lista de Verificação  

| Tipo de listagem | Tipo de oferta | Artefacto editorial |   
|:--- |:--- |:--- |  
| Todos | Todos | <table> <tr><th>Detalhes da montra</th></tr> <tr><td>Nome da oferta (200 caracteres)</td></tr> <tr><td>Descrição (2.000 caracteres)</td></tr> <tr><td>ID do MPN</td></tr> <tr><td>Disponibilidade país/região</td></tr> <tr><td>Indústrias, categorias e palavras-chave de pesquisa aplicáveis</td></tr> <tr><td>Screenshots (1280x720; máximo 5)</td></tr> <tr><td>Documentos de marketing (máximo 3)</td></tr> <tr><td>Destino de chumbo</td></tr> <tr><td>Vídeo de visão geral do produto (opcional)</td></tr> </table> <table> <tr><th>Contactos</th></tr> <tr><td>Informações de contacto (apoio, engenharia, comercial)</td></tr> </table> <table> <tr><th>Informação Técnica</th></tr> <tr><td>Termos de utilização e URL de política de privacidade</td></tr> </table> <table> <tr><th>Versão de Teste</th></tr> <tr><td>Nome do grupo de recursos do Azure</td></tr> </table> |  
| Todos | Máquina Virtual | <table> <tr><th>Informação Técnica</th></tr> <tr><td>URL de suporte</td></tr> </table> |
| Lista | Serviço de consultoria | <table> <tr><th>Detalhes da montra</th></tr> <tr><td>Duração do noivado</td></tr> <tr><td>Logotipos da empresa (48x48, 216x216)</td></tr> </table> |  
| Avaliação | Todos | <table> <tr><th>Informação Técnica</th></tr> <tr><td>URL de teste</td></tr> <tr><td>Linguagens suportadas</td></tr> <tr><td>Número da versão da aplicação</td></tr> <tr><td>Data de lançamento da aplicação</td></tr> <tr><td>URL de suporte</td></tr> </table> |  
| Avaliação | Test drive | <table> <tr><th>Versão de Teste</th></tr> <tr><td>Descrição</td></tr> <tr><td>Duração</td></tr> <tr><td>Manual do utilizador</td></tr> <tr><td>Vídeo de test drive (máximo 1)</td></tr> <tr><td>Test drive país/região disponibilidade</td></tr> <tr><td>ID de subscrição azure</td></tr> <tr><td>ID de inquilino do Azure AD</td></tr> <tr><td>Id da aplicação Azure AD</td></tr> <tr><td>Chave da aplicação Azure AD</td></tr> </table> |  
| Transt | Máquina Virtual | <table> <tr><th>Storefronts</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Detalhes do sistema operativo</td></tr> <tr><td>Portos em uso</td></tr> <tr><td>Protocolos em uso</td></tr> <tr><td>Versão em disco para cada VHD em uso</td></tr> <tr><td>URL SAS para cada VHD em uso</td></tr> </table> |  
| Transt | Aplicativos Azure: Modelo de solução | <table> <tr><th>Storefronts</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Número da versão</td></tr> <tr><td>Ficheiro de pacote que contém<ul> <li>todos os ficheiros de modelo</li> <li>createUIDefinition file</li> </ul> </td></tr> </table> |  
| Transt | Aplicativos Azure: App gerida | <table> <tr><th>Storefronts</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Número da versão</td></tr> <tr><td>Ficheiro de pacote que contém<ul> <li>todos os ficheiros de modelo</li> <li>createUIDefinition file</li> </ul> </td></tr> </table> |  
| Transt | Contentor | <table> <tr><th>Storefronts</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Registo de contentores Azure (ACR) Detalhes do repositório de imagem: ID de subscrição</td></tr> <tr><td>Detalhes do repositório de imagem ACR: Nome do grupo de recursos</td></tr> <tr><td>Detalhes do repositório de imagem ACR: Nome do registo</td></tr> <tr><td>Detalhes do repositório da Imagem ACR: Nome repositório</td></tr> <tr><td>Detalhes do repositório de imagem ACR: Nome do utilizador</td></tr> <tr><td>Detalhes do repositório de imagem ACR: Palavra-passe</td></tr> <tr><td>Detalhes do repositório de imagem ACR: Etiquetas de imagem (opcional)</td></tr> </table> |  
| Transt | App SaaS | <table> <tr><th>Storefronts</th></tr> <tr><td>Título (50 caracteres)</td></tr> <tr><td>Resumo (200 caracteres)</td></tr> <tr><td>Resumo longo (256 caracteres)</td></tr> <tr><td>Descrição baseada em HTML (3.000 caracteres)</td></tr> <tr><td>Logotipos da empresa (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> |  

## <a name="next-steps"></a>Passos seguintes
*   Visite a página do Guia da [Editora Azure Marketplace e AppSource.](./marketplace-publishers-guide.md)  
 
---
