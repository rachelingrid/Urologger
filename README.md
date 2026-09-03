# Controle de Diurese Domiciliar

Aplicativo web para acompanhamento do débito urinário de pacientes em uso de coletor no domicílio. O cuidador fotografa o coletor a cada esvaziamento e registra volume, aspecto e queixas; o aplicativo calcula o débito em mL/h e mL/kg/h.

O princípio é simples: cada esvaziamento é um ponto de medida. Quanto mais frequentes os registros, menor a janela de incerteza e mais fiel a estimativa de fluxo urinário. Um cuidador que despreza o coletor quatro vezes ao dia obtém um retrato razoável; oito vezes, um retrato bom.

## Por que existe

O débito urinário é um dos sinais mais precoces de deterioração clínica e, no ambiente hospitalar, é medido de hora em hora. No domicílio, quase sempre se perde: o cuidador esvazia o coletor, olha, e a informação evapora. Sem registro estruturado, a família relata "urinou pouco hoje" e a equipe não tem como qualificar isso.

Este aplicativo transforma um gesto que já acontece — esvaziar o coletor — em dado clínico rastreável, sem acrescentar tarefa ao cuidador.

## O que registra

**Paciente** — nome, data de nascimento, peso, altura e capacidade do coletor. O peso é o que permite converter mL/h em mL/kg/h e aplicar o limiar de oligúria.

**A cada esvaziamento**

- Foto do coletor, com bolha de nível e guia de enquadramento na tela
- Data e hora, preenchidas automaticamente
- Volume lido na graduação do coletor
- Aspecto, em escala de sete tons — da urina incolor à cor de borra de vinho
- Odor, em seis categorias
- Queixas do paciente: ardência, dor suprapúbica, dor lombar, náusea, vômito, distensão abdominal, febre, confusão
- Observações livres do cuidador

**Sinalizações automáticas**

- Hematúria macroscópica, quando o aspecto é marcado nos dois tons mais escuros
- Suspeita de infecção, quando febre ou ardência coincidem com urina alterada
- Suspeita de obstrução da sonda, quando há distensão abdominal com coletor pouco cheio
- Oligúria, quando o débito cai abaixo de 0,5 mL/kg/h

## Como funciona o cálculo

O primeiro registro do dia marca o início da janela e não entra na soma — o volume contido nele acumulou-se durante um período não medido, e contá-lo inflaria o resultado. Os registros seguintes somam-se e dividem-se pelas horas decorridas desde aquele marco zero.

A leitura do volume é feita pelo cuidador na graduação do próprio coletor. A foto serve para o aspecto, para a auditoria posterior pela equipe e para constituir o banco de imagens pareado a volumes reais que uma futura detecção automática de nível exigiria.

## Instalação

O aplicativo é publicado pelo GitHub Pages e não depende de servidor, banco de dados ou qualquer serviço externo.

1. Abra o endereço do Pages no navegador do celular
2. Android: menu do Chrome, *Instalar app*. iOS: compartilhar, *Adicionar à Tela de Início*
3. A partir daí abre em tela cheia e funciona sem conexão

Câmera e sensor de inclinação exigem HTTPS, disponível pelo Pages. Abrir o arquivo diretamente do armazenamento do aparelho desabilita essas funções.

## Privacidade

Nenhum dado sai do aparelho. Registros, fotos e dados do paciente ficam no armazenamento local do navegador, e não há qualquer transmissão para servidores. A exportação em CSV é manual e fica sob controle de quem opera o aplicativo. Como consequência, desinstalar o aplicativo ou limpar os dados do navegador apaga o histórico — exporte periodicamente.

## Limitações

O volume não é estimado pela imagem. Ângulo, distância, iluminação, transparência do plástico e dobras do coletor produzem erro grande e inconsistente; a leitura humana na graduação é mais confiável e mais rápida.

A sugestão automática de aspecto compara a cor média da região central da foto com a escala de referência. É uma sugestão, sempre confirmada ou corrigida pelo cuidador, nunca um veredito.

Os limiares de débito e a escala de aspecto seguem referências clínicas gerais e não foram validados para este aplicativo. Ele é uma ferramenta de registro e de alerta para o cuidador, não um dispositivo diagnóstico, e não substitui a avaliação da equipe assistente.

## Estrutura

```
index.html      aplicativo completo — interface, lógica e armazenamento
sw.js           service worker, responsável pelo funcionamento offline
manifest.json   metadados de instalação na tela inicial
```

Ao alterar o `index.html`, incremente a versão do cache no `sw.js` (`diurese-v1` → `diurese-v2`). Sem isso, os aparelhos continuam servindo a versão antiga.

## Licença

MIT.
