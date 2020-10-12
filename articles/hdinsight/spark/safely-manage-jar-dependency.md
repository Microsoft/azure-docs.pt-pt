---
title: Gerir com segurança as dependências dos frascos - Azure HDInsight
description: Este artigo discute as melhores práticas para gerir as dependências do Java Archive (JAR) para aplicações HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: b5b8c014a7150ad83875b9fd361c3538d865d153
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064155"
---
# <a name="safely-manage-jar-dependencies"></a>Gerir dependências jar com segurança

Os componentes instalados em clusters HDInsight têm dependências de bibliotecas de terceiros. Normalmente, uma versão específica de módulos comuns como o Guava é referenciada por estes componentes incorporados. Quando submete uma aplicação com as suas dependências, pode causar um conflito entre diferentes versões do mesmo módulo. Se a versão componente que se refere primeiro no classpath, os componentes incorporados podem lançar exceções devido à incompatibilidade da versão. No entanto, se os componentes incorporados injetarem as suas dependências no caminho de classe primeiro, a sua aplicação pode lançar erros como `NoSuchMethod` .

Para evitar conflitos de versão, considere sombrear as dependências da sua aplicação.

## <a name="what-does-package-shading-mean"></a>O que significa sombreamento de pacote?
Sombreamento fornece uma maneira de incluir e renomear dependências. Desloca as classes e reescreve o código e os recursos afetados para criar uma cópia privada das suas dependências.

## <a name="how-to-shade-a-package"></a>Como fazer sombra a um pacote?

### <a name="use-uber-jar"></a>Use o jarro uber
O uber-jar é um único ficheiro de frasco que contém tanto o frasco de aplicação como as suas dependências. As dependências do frasco uber não são sombreadas por padrão. Em alguns casos, isto pode introduzir conflito de versão se outros componentes ou aplicações referenciar uma versão diferente dessas bibliotecas. Para evitar isto, pode construir um ficheiro Uber-Jar com algumas (ou todas) dependências sombreadas.

### <a name="shade-package-using-maven"></a>Pacote de sombra usando Maven
Maven pode construir aplicações escritas tanto em Java como em Scala. Maven-shade-plugin pode ajudá-lo a criar um uber-jar sombreado facilmente.

O exemplo abaixo mostra um ficheiro `pom.xml` que foi atualizado para ensombrar um pacote utilizando plugin maven-shade.  A secção XML `<relocation>…</relocation>` desloca as classes de pacote para pacote `com.google.guava` `com.google.shaded.guava` movendo as entradas de ficheiros JAR correspondentes e reescrevendo o bytecode afetado.

Depois de `pom.xml` mudar, pode executar `mvn package` para construir o uber-jar sombreado.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Pacote de sombra usando SBT
A SBT é também uma ferramenta de construção para Scala e Java. SBT não tem um plugin de sombra como maven-shade-plugin. Pode modificar `build.sbt` o ficheiro para fazer sombra. 

Por exemplo, para fazer `com.google.guava` sombra, pode adicionar o comando abaixo ao `build.sbt` ficheiro:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Depois podes correr `sbt clean` e construir o ficheiro do frasco `sbt assembly` sombreado. 

## <a name="next-steps"></a>Passos seguintes

* [Use ferramentas HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Criar uma aplicação Scala Maven para Faísca em IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
