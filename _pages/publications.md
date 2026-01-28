---
permalink: /publications/
title: "Publications"
layout: single
toc: true
toc_sticky: true
---

<script>
document.addEventListener('DOMContentLoaded', function() {
  var total = document.querySelectorAll('ol.bibliography li').length + 1;
  document.querySelector('.page__content').style.counterReset = 'publication ' + total;
});
</script>

## 2025
{% bibliography --query @*[year=2025] %}

## 2024
{% bibliography --query @*[year=2024] %}

## 2023
{% bibliography --query @*[year=2023] %}

## 2022
{% bibliography --query @*[year=2022] %}

## 2021
{% bibliography --query @*[year=2021] %}

## 2020
{% bibliography --query @*[year=2020] %}

## 2019
{% bibliography --query @*[year=2019] %}

## 2018
{% bibliography --query @*[year=2018] %}

## 2016
{% bibliography --query @*[year=2016] %}

## 2013
{% bibliography --query @*[year=2013] %}

## 2012
{% bibliography --query @*[year=2012] %}

## 2011
{% bibliography --query @*[year=2011] %}

## 2010
{% bibliography --query @*[year=2010] %}

<!--
Your old manual publication list has been backed up to _pages/publications.md.backup
Once you've fully migrated to the BibTeX workflow, you can delete that backup.
-->
