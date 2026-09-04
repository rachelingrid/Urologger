# Urologger — Controle de Diurese Domiciliar

Aplicativo web para acompanhamento do débito urinário de pacientes em uso de coletor no domicílio. O cuidador fotografa o coletor a cada esvaziamento; o aplicativo lê a graduação, registra aspecto e queixas, e calcula o débito em mL/h e mL/kg/h.

O princípio é simples: cada esvaziamento é um ponto de medida. Quanto mais frequentes os registros, menor a janela de incerteza e mais fiel a estimativa de fluxo urinário. Um cuidador que despreza o coletor quatro vezes ao dia obtém um retrato razoável; oito vezes, um retrato bom.

## Por que existe

O débito urinário é um dos sinais mais precoces de deterioração clínica, e no ambiente hospitalar é medido de hora em hora. No domicílio, quase sempre se perde: o cuidador esvazia o coletor, olha, e a informação evapora. Sem registro estruturado, a família relata "urinou pouco hoje" e a equipe não tem como qualificar isso.

Este aplicativo transforma um gesto que já acontece — esvaziar o coletor — em dado clínico rastreável, sem acrescentar tarefa ao cuidador.

## Cadastros

**Paciente** — nome, data de nascimento, peso e altura. O peso é o que permite converter mL/h em mL/kg/h e aplicar o limiar de oligúria.

**Modelos de coletor** — cadastrados uma vez cada, com capacidade total e valor entre marcas da graduação. Essa constante é o que dá escala absoluta à leitura automática. A partir do cadastro, toda foto daquele modelo é medida sem intervenção.

## Aquisição da imagem

Antes de abrir a câmera, o aplicativo apresenta as condições de captura: coletor alinhado e sem dobras, flash em ambiente escuro, câmera perpendicular ao coletor na altura do líquido, bordas laterais dentro do enquadramento, disparo firme. O cuidador pode dispensar o lembrete depois de habituado.

Durante a captura, o acelerômetro alimenta uma bolha de nível que indica quando o aparelho está aprumado, e uma silhueta na tela normaliza o enquadramento. A orientação é fixa em retrato.

## Leitura da graduação

Três etapas independentes, cada uma com critério próprio de rejeição.

**Linha do líquido** — perfil vertical de saturação e luminância na região do coletor. A interface entre urina e ar aparece como degrau, porque a urina é cromaticamente saturada e o plástico vazio não.

**Marcas da graduação** — autocorrelação do gradiente vertical, que devolve o espaçamento entre traços em pixels. Esse espaçamento é a escala absoluta daquela imagem específica, o que torna a medida independente de distância, ângulo e enquadramento.

**Volume** — marcas contadas entre a base e o nível, multiplicadas pelo valor da marca do modelo cadastrado.

Quando qualquer etapa falha — marcas ilegíveis, contraste insuficiente entre líquido e plástico, ou leitura acima da capacidade —, o aplicativo informa qual condição não foi satisfeita e oferece a marcação manual por três toques: base, marca superior, nível. O resultado é desenhado sobre a foto para conferência antes de salvar, e a procedência de cada medida (graduação, marcação ou digitação) fica gravada no registro.

## Data e hora

Foto tirada no momento: instante do disparo. Foto escolhida da galeria: data original contida no arquivo (EXIF) e, na ausência dela, o padrão de data presente no nome do arquivo, que cobre os formatos usuais de câmera Android, Pixel e WhatsApp. Sem nenhuma das duas, o campo é sinalizado para ajuste manual — um erro de horas destrói o cálculo de mL/h e não deve passar despercebido.

## Registro clínico

A cada esvaziamento: volume, aspecto em escala de sete tons (sugerido pela cor média da imagem e confirmado pelo cuidador), odor em seis categorias, queixas do paciente em lista numerada — ardência, dor suprapúbica, dor lombar, náusea, vômito, distensão abdominal, febre, confusão — e observações livres.

Sinalizações automáticas: hematúria macroscópica pelo aspecto; suspeita de infecção quando febre ou ardência coincidem com urina alterada; suspeita de sonda obstruída quando há distensão abdominal com coletor pouco cheio; oligúria abaixo de 0,5 mL/kg/h.

## Cálculo do débito

O primeiro registro do dia marca o início da janela e não entra na soma — o volume contido nele acumulou-se durante um período não medido, e contá-lo inflaria o resultado. Os registros seguintes somam-se e dividem-se pelas horas decorridas desde aquele marco.

## Saídas

**Relatório em PDF** — período, identificação do paciente, indicadores de síntese, gráfico do débito diário em vetor, tabela por dia, contagem de queixas e a relação completa de registros com a cor de cada aspecto. Gerado pela impressão do navegador, com a opção *Salvar como PDF* do próprio aparelho.

**CSV** — todos os registros, incluindo procedência e confiança de cada medida.

## Instalação

O aplicativo é publicado pelo GitHub Pages e não depende de servidor, banco de dados ou qualquer serviço externo.

1. Abra o endereço do Pages no navegador do celular
2. Android: menu do Chrome, *Instalar app*. iOS: compartilhar, *Adicionar à Tela de Início*
3. A partir daí abre em tela cheia e funciona sem conexão

Câmera e sensor de inclinação exigem HTTPS, disponível pelo Pages. Abrir o arquivo diretamente do armazenamento do aparelho desabilita essas funções.

## Privacidade

Nenhum dado sai do aparelho. Registros, fotos e dados do paciente ficam no armazenamento local do navegador, e não há qualquer transmissão para servidores. As exportações são manuais e ficam sob controle de quem opera o aplicativo. Como consequência, desinstalar o aplicativo ou limpar os dados do navegador apaga o histórico — exporte periodicamente.

## Limitações

**A leitura automática não foi validada em campo.** Os limiares de detecção foram calibrados em imagens sintéticas, que têm geometria limpa. Coletor real apresenta dobras, reflexo, condensação e marcas impressas que podem desaparecer contra fundo claro. O aplicativo prefere rejeitar a leitura a produzir um número duvidoso, mas os limiares provavelmente precisarão de ajuste após uso real.

**Não há leitura óptica dos números impressos.** A periodicidade das marcas já fornece a escala, o que torna o número redundante; reconhecimento de dígitos pequenos em plástico curvo e translúcido, sem biblioteca externa, falharia justamente nas condições de uso.

**Os valores de referência são padrões clínicos gerais** e não foram validados para este aplicativo. Trata-se de ferramenta de registro e alerta para o cuidador, não de dispositivo diagnóstico, e não substitui a avaliação da equipe assistente.

## Estrutura

```
index.html      aplicativo completo — interface, visão computacional, armazenamento e relatório
sw.js           service worker, responsável pelo funcionamento offline
manifest.json   metadados de instalação na tela inicial
```

Ao alterar o `index.html`, incremente a versão do cache no `sw.js` (`diurese-v6` → `diurese-v7`). Sem isso, os aparelhos continuam servindo a versão antiga.

## Licença

MIT.
