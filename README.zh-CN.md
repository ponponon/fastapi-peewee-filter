

# fastapi-peewee-filter


[简体中文](./README.zh-CN.md) | [English](./README.md)

## 简介

参考：https://github.com/arthurio/fastapi-filter

但是这个 fastapi-filter 只支持 SQLAlchemy，所以我搞了一个 fastapi-peewee-filter 用于支持 peewee 这个 ORM

## 安装

Python 解释器:

- CPython : 3.8 及以上版本

## 用法


```python
from fastapi import Depends
from fastapi_peewee_filter import BaseFilter
from dataclasses import dataclass, field
from datetime import datetime
from loguru import logger
from fastapi import APIRouter
from fastapi import Query
from fastapi.requests import Request
from core.mysql.models import SeriesTable, TrackingwebsiteTable

series = APIRouter(tags=["GS review"], prefix='/series')


@dataclass
class SeriesFilter(BaseFilter):
    assignee_in: str | None = None
    company_id_in: str | None = None
    company_id_nin: str | None = None
    imdbid_status_in: str | None = None
    confirm_at_gte: datetime | None = None
    confirm_at_lte: datetime | None = None
    created_at_gte: datetime | None = None
    created_at_lte: datetime | None = None
    rating_score_gte: int | None = None
    rating_score_lte: int | None = None

    class Meta:
        model_class = SeriesTable


@series.get('', summary='list series')
def list_series(
    series_filter: SeriesFilter = Depends(SeriesFilter),
    page_size: int = Query(10),
    offset: int = Query(0)
):
    q = series_filter.filter()

    q = q.offset(offset).limit(page_size)

    return list(q.dicts())
```