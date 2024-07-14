---
layout: page
title: Rare Words Finder
description: App that highlights rare words in a text
img: assets/img/rareWordsFinder.png
importance: 2
category: Apps
giscus_comments: false
related_publications: false
---

<h1>{{ page.title }}</h1>

<textarea id="text-input" placeholder="Paste your text here..."></textarea>
<div id="slider-container">
    <label for="num-words-slider" id="slider-label">Number of rare words to highlight: </label>
    <input type="range" id="num-words-slider" min="1" max="50" value="10">
    <span id="slider-value">10</span>
</div>
<div id="highlighted-text"></div>
<table id="results-table">
    <thead>
        <tr>
            <th>Word</th>
            <th>Frequency</th>
            <th>Zipf Ranking</th>
        </tr>
    </thead>
    <tbody></tbody>
</table>

<style>
    body {
        font-family: Arial, sans-serif;
        margin: 20px;
    }
    textarea {
        width: 100%;
        height: 200px;
        font-size: 14px;
        padding: 10px;
        box-sizing: border-box;
        margin-bottom: 10px;
        border: 1px solid #ccc;
        border-radius: 4px;
    }
    #highlighted-text {
        border: 1px solid #ccc;
        padding: 10px;
        border-radius: 4px;
        white-space: pre-wrap;
        margin-bottom: 20px;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
    }
    table, th, td {
        border: 1px solid black;
    }
    th, td {
        padding: 8px;
        text-align: left;
    }
    .highlight {
        background-color: yellow;
        color: black;
    }
    #slider-container {
        display: flex;
        align-items: center;
        margin-bottom: 20px;
    }
    #slider-label {
        margin-right: 10px;
    }
</style>

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/wordfreq@2.5.0/src/wordfreq.worker.min.js"></script>
<script>
    const wordfreq = WordFreq();

    $(document).ready(function() {
        function processText() {
            const text = $('#text-input').val();
            const numWords = $('#num-words-slider').val();

            wordfreq.process(text, (list) => {
                const sorted_unique_words = list.map(item => item[0]);
                const word_freqs = Object.fromEntries(list.map(item => [item[0], item[1]]));

                const rarest_words = sorted_unique_words.slice(0, numWords);
                const table_data = sorted_unique_words.map(word => {
                    const frequency = word_freqs[word];
                    const zipf = Math.log10(frequency / 1000);
                    return [word, frequency.toExponential(1), zipf.toFixed(2)];
                });

                highlightText(text, rarest_words);
                displayTable(table_data);
            });
        }

        $('#text-input').on('input', processText);

        $('#num-words-slider').on('input', function() {
            $('#slider-value').text($(this).val());
            processText();
        });

        // Initial process
        processText();
    });

    function highlightText(text, rarestWords) {
        let highlightedText = text;
        rarestWords.forEach(function(word) {
            const regex = new RegExp(`\\b${word}\\b`, 'gi');
            highlightedText = highlightedText.replace(regex, `<span class="highlight">${word}</span>`);
        });
        $('#highlighted-text').html(highlightedText.replace(/\n/g, '<br>'));
    }

    function displayTable(data) {
        const tableBody = $('#results-table tbody');
        tableBody.empty();
        data.forEach(function(row) {
            const tr = $('<tr></tr>');
            row.forEach(function(cell) {
                const td = $('<td></td>').text(cell);
                tr.append(td);
            });
            tableBody.append(tr);
        });
    }
</script>
