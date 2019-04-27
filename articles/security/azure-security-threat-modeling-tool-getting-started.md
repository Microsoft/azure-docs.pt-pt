---
title: Introdução ao Azure - ferramenta de modelagem de ameaças Microsoft - | Documentos da Microsoft
description: Esta é uma visão mais profunda Realce a ferramenta de modelagem de ameaças em ação.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 6315e6d39a3b68854beb6563d075e3c79ca93a69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610789"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Guia de introdução a ferramenta de modelagem de ameaças

2018 a ferramenta Microsoft de modelagem de ameaças lançada como disponibilidade geral, em Setembro de 2018, como um livre  **[clique para download](https://aka.ms/threatmodelingtool)**. A alteração no mecanismo de entrega permite-nos enviar as mais recentes melhorias e correções de erros para os clientes sempre que abrem a ferramenta, tornando mais fácil de manter e utilizar.
Este artigo orienta-o processo de introdução com a abordagem de modelagem de ameaças do Microsoft SDL e mostra-lhe como utilizar a ferramenta para desenvolver ótimos modelos de risco como um backbone de seu processo de segurança.

Este artigo baseia-se no conhecimento existente de abordagem de modelagem de ameaças SDL. Para uma análise rápida, consulte **[aplicativos de Web de modelagem de ameaças](https://msdn.microsoft.com/library/ms978516.aspx)** e uma versão arquivada do **[descobrir segurança falhas usando a abordagem STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** Artigo do MSDN publicado em 2006.

Para resumir rapidamente, a abordagem envolve a criação de um diagrama, identificar ameaças, minimizá-los e a validar cada redução. Este é um diagrama que realça este processo:

![Processo do SDL](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>A iniciar o processo de modelagem de ameaças

Quando inicia a ferramenta de modelagem de ameaças, perceberá que algumas coisas, como mostrado na imagem:

![Página inicial em branco](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Secção de modelo de ameaças

| Componente                                   | Detalhes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Comentários, sugestões e botão de problemas** | Leva-o **[fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** para todos os aspetos SDL. Ele fornece uma oportunidade para ler o que outros usuários estão fazendo, juntamente com as soluções alternativas e recomendações. Se ainda não é possível encontrar o que está procurando, envie um e-mail tmtextsupport@microsoft.com para a nossa equipa de suporte para o ajudar a                                                                                                                            |
| **Criar um modelo**                          | Abre uma tela em branco para que possa desenhar seu diagrama. Lembre-se de que selecionam o modelo que pretende utilizar para o seu modelo                                                                                                                                                                                                                                                                                                                                                                       |
| **Modelo para novos modelos**                 | Tem de selecionar o modelo a utilizar antes de criar um modelo. Nosso modelo principal é o modelo de modelo de ameaças do Azure, que contém os estênceis específicos do Azure, ameaças e atenuações. Para modelos genéricos, selecione a Base de dados de conhecimento do SDL TM no menu pendente. Quer criar seu próprio modelo ou submeta um novo para todos os utilizadores? Confira nosso **[repositório de modelos](https://github.com/Microsoft/threat-modeling-templates)** página do GitHub para saber mais                              |
| **Abra um modelo**                            | <p>Abre-se tinha guardado modelos de ameaças. A funcionalidade de modelos para a abertos recentemente é ótima se precisa abrir os ficheiros mais recentes. Quando paira o rato sobre a seleção, encontrará 2 formas de abrir modelos:</p><p><ul><li>Abra partir deste computador – modo clássico de abrir um ficheiro com o armazenamento local</li><li>Abra do OneDrive – as equipes podem usar pastas no OneDrive para guardar e partilhar a todos os seus modelos de ameaças num único local para o ajudar a aumentar a produtividade e colaboração</li></ul></p> |
| **Guia de introdução**                   | Abre o **[Microsoft Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** página principal                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Secção de modelo

| Componente               | Detalhes                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criar novo modelo** | Abre-se um modelo em branco para que possa criar no. A menos que tenha o vasto conhecimento na criação de modelos do zero, recomendamos a criação de aplicações existentes |
| **Modelo aberto**       | Abre-se de que existente modelos para fazer alterações para                                                                                                              |

A equipe de ferramenta de modelagem de ameaças está trabalhando constantemente para melhorar a funcionalidade da ferramenta e experiência. Algumas pequenas alterações, poderão ocorrer ao longo do ano, mas todas as principais alterações requerem reescritas no guia. Fazer referência a ele, muitas vezes, para garantir que obtém os anúncios mais recentes.

## <a name="building-a-model"></a>Criar um modelo

Nesta secção, vamos seguir:

- Cristina (desenvolvedor)
- Ricardo (gerente de programas) e
- Ashish (um testador)

Que passam pelo processo de desenvolvimento de seu primeiro modelo de risco.

> Ricardo: Olá Cristina, eu trabalhados o diagrama do modelo de risco e queria certificar-se de que temos os detalhes da direita. Vocês podem me ajudar procurá-lo ao longo do?
> Cristina: Com certeza. Vamos dar uma olhada.
> Ricardo abre-se a ferramenta e compartilha seu ecrã com Cristina.

![Modelo de ameaças básico](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, parece simples, mas examiná-me-lo?
> Ricardo: -Se! Eis a divisão:
> - Nosso utilizador humano é desenhado como uma entidade externa — um quadrado
> - Estão a enviar comandos para o nosso servidor Web — o círculo
> - O servidor Web está consultando um banco de dados (por duas linhas paralelas)

O que Ricardo acabamos de mostrar Cristina é a abreviação de um DFD  **[diagrama de fluxo de dados](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A ferramenta de modelagem de ameaças permite aos utilizadores especificar limites de fidedignidade, indicados pelas linhas pontilhadas vermelhas, para mostrar onde as diferentes entidades estão no controle. Por exemplo, os administradores de TI exigem um sistema do Active Directory para fins de autenticação, para que o Active Directory está fora do controlo.

> Cristina: Tem o aspeto pretendido para mim. E as ameaças?
> Ricardo: Deixe-me mostrar a.

## <a name="analyzing-threats"></a>Análise de riscos

Assim que ele clica na vista de análise da seleção de menu de ícone (arquivo com Lupa), ele é levado para uma lista de ameaças geradas a ferramenta de modelagem de ameaças encontradas com base no modelo padrão, que utiliza a abordagem do SDL chamada  **[ STRIDE (Spoofing, violação, divulgação de informações, rejeição, Denial of Service e elevação de privilégios)](https://en.wikipedia.org/wiki/STRIDE_(security))**. A idéia é que o software é fornecido um conjunto previsível de ameaças, que podem ser encontrados usando essas 6 categorias.

Essa abordagem é como a proteção da sua casa, assegurando que cada porta e janela tem um mecanismo de bloqueio em vigor antes de adicionar um sistema de alarme ou perseguir o ladrão.

![Ameaças básicas](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo começa ao selecionar o primeiro item na lista. Eis o que acontece:

Em primeiro lugar, a interação entre os dois estênceis foi melhorada

![Interação](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

Segundo, mais informações sobre a threat aparecem na janela de propriedades de ameaças

![Informações de interação](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

A ameaça gerada ajuda-lo a compreender os potenciais falhas de design. A categorização de STRIDE dá em contato com ele uma idéia sobre potenciais vetores de ataque, enquanto a descrição adicional informa-o exatamente o que está errado, juntamente com potenciais formas de resolver. Ele pode usar campos editáveis para escrever notas nos detalhes de justificação ou alterar as classificações de prioridade dependendo da barra de bugs da sua organização.

Modelos do Azure tem detalhes adicionais para ajudar os utilizadores a compreender não apenas o que está errado, mas também como corrigi-lo ao adicionar descrições, exemplos e hiperligações à documentação específica do Azure.

A descrição feita em contato com ele percebe a importância da adição de um mecanismo de autenticação para impedir que os utilizadores a ser falsificado, revelando o primeiro risco ser abordado. Alguns minutos para a discussão com Cristina, compreendia a importância da implementação de controlo de acesso e funções. Ricardo preenchidos algumas notas rápidas para se certificar de que estes foram implementadas.

Como Ricardo foram consideradas as ameaças de divulgação de informações, Paul percebeu que o plano de controlo de acesso necessário a algumas contas somente leitura para auditoria e geração de relatórios. Ele identificaria se deve ser uma ameaça de novo, mas as atenuações eram os mesmos, para que ele indicados a ameaça em conformidade.
Ele também pensamos divulgação de informações mais um pouco e percebi que as bandas de cópia de segurança foram vai precisar de encriptação, uma tarefa para a equipe de operações.

As garantias de ameaças não aplicáveis para o design devido a atenuações existentes ou de segurança pode ser alterado para "Não aplicável" na lista suspensa de estado. Há três outras opções: A seleção predefinida, tem de investigação – não iniciado – utilizado para dar seguimento itens e Mitigated – assim que ele é totalmente trabalhado.

## <a name="reports--sharing"></a>Relatórios e de partilha

Depois de Ricardo passa pela lista com Cristina e adiciona notas importantes, atenuações/justificativas, prioridade e as alterações de estado, seleciona de he relatórios -> Criar relatório completo -> Guardar relatório, que imprime um ótimo relatório por ele passar com colegas para garantir que o trabalho de segurança adequada é implementado.

![Informações de interação](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Se o Ricardo quer partilhar o ficheiro em vez disso, ele pode facilmente fazê-lo sejam guardados na conta do OneDrive da sua organização. Assim que ele faz isso, ele pode copiar a ligação para documento e partilhá-lo com colegas. 

## <a name="threat-modeling-meetings"></a>Reuniões para modelagem de ameaças

Quando o Ricardo enviado seu modelo de risco para o seu colega usando o OneDrive, Ashish, o testador, foi underwhelmed. Pareceu Ricardo e Cristina perdeu algumas importante nos casos extremos, que poderia ser facilmente comprometidas. Seu ceticismo é um complemento aos modelos de risco.

Neste cenário, depois de Ashish assumiu o modelo de risco, ele chamou para duas reuniões para modelagem de ameaças: uma reunião para sincronizar o processo e analisar os diagramas e, em seguida, uma segunda reunião para ameaças revisão e aprovação.

Na primeira reunião, Ashish passou 10 minutos descrevendo todas as pessoas por meio do processo de modelagem de risco do SDL. Em seguida, ele apresentou o diagrama do modelo de risco e começou a explicá-lo em detalhes. Dentro de cinco minutos, um componente ausente importante já havia sido identificado.

Alguns minutos mais tarde, Ashish e Ricardo iniciaram uma longa discussão sobre como o servidor Web foi criado. Não era a forma ideal de uma reunião continuar, mas todos concordaram que descobrir a discrepância no início foi vai poupá-los no futuro de tempo.

Na segunda reunião, a equipe mostrou como analisou os riscos, discutiu algumas formas de lidar com eles e terminou a sessão sobre o modelo de ameaça. Eles colocaram o documento em controle de origem e continuaram o desenvolvimento.

## <a name="thinking-about-assets"></a>Pensando nos ativos

Alguns leitores que já fizeram modelos de risco podem notar que não falamos sobre os recursos de todo. Descobrimos que muitos engenheiros de software entendem de melhor do que eles compreendem o conceito de ativos e quais ativos um invasor poderá estar interessado no seu software.

Se vai para o modelo de risco de uma casa, poderá começar pensando sua família, insubstituíveis fotos ou obras de arte. Talvez comece pensando que poderá danificar e o sistema de segurança atual. Ou pode também começar levando as características físicas, como o conjunto piscina ou a. Isso é o mesmo que pensar em ativos, invasores ou design de software. Qualquer uma dessas abordagens funcionará.

A abordagem que Apresentamos aqui de modelagem de risco é consideravelmente mais simples do que o que a Microsoft fez no passado. Descobrimos que a abordagem de design de software funciona bem para muitas equipes. Esperamos que seja uma delas.

## <a name="next-steps"></a>Próximos Passos

Envie suas dúvidas, comentários e preocupações para tmtextsupport@microsoft.com. **[Baixe](https://aka.ms/threatmodelingtool)**  a ferramenta de modelagem de ameaças para começar a utilizar.
