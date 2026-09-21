-- ==========================================
-- SMART HOME DATABASE SCHEMA
-- Tables:
-- 1) rooms
-- 2) devices
-- 3) device_states
-- 4) sensor_readings
-- ==========================================

-- เปิดใช้งาน UUID generator (ปกติมีอยู่แล้วใน Supabase)
create extension if not exists "pgcrypto";

-- ==========================================
-- 1) ตารางห้อง
-- ==========================================
create table if not exists public.rooms (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  description text,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now(),

  constraint rooms_name_unique unique (name)
);

-- ==========================================
-- 2) ตารางอุปกรณ์
-- device_type ตัวอย่าง:
-- light, fan, relay, door_lock, sensor
-- ==========================================
create table if not exists public.devices (
  id uuid primary key default gen_random_uuid(),
  room_id uuid references public.rooms(id) on delete set null,
  name text not null,
  device_type text not null default 'relay',
  icon text not null default '🔌',
  mqtt_topic text unique,
  is_online boolean not null default true,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

create index if not exists devices_room_id_idx
on public.devices(room_id);

create index if not exists devices_device_type_idx
on public.devices(device_type);

-- ==========================================
-- 3) ตารางสถานะล่าสุดของอุปกรณ์
-- 1 device มีได้ 1 current state
-- ใช้กับการเปิด/ปิดจาก Dashboard
-- ==========================================
create table if not exists public.device_states (
  id uuid primary key default gen_random_uuid(),
  device_id uuid not null unique references public.devices(id) on delete cascade,
  is_on boolean not null default false,
  value jsonb not null default '{}'::jsonb,
  updated_at timestamptz not null default now()
);

create index if not exists device_states_device_id_idx
on public.device_states(device_id);

-- ==========================================
-- 4) ตารางข้อมูลจากเซนเซอร์ตามเวลา
-- ค่าแต่ละตัวเป็น optional เพื่อรองรับ
-- เซนเซอร์คนละชนิดกันได้
-- ==========================================
create table if not exists public.sensor_readings (
  id bigint generated always as identity primary key,
  device_id uuid references public.devices(id) on delete set null,
  temperature numeric(5, 2),
  humidity numeric(5, 2),
  light integer,
  motion boolean,
  created_at timestamptz not null default now()
);

create index if not exists sensor_readings_device_id_idx
on public.sensor_readings(device_id);

create index if not exists sensor_readings_created_at_idx
on public.sensor_readings(created_at desc);

-- ==========================================
-- อัปเดต updated_at ให้อัตโนมัติเมื่อแก้ไขข้อมูล
-- ==========================================
create or replace function public.set_updated_at()
returns trigger
language plpgsql
security invoker
as $$
begin
  new.updated_at = now();
  return new;
end;
$$;

drop trigger if exists set_rooms_updated_at on public.rooms;
create trigger set_rooms_updated_at
before update on public.rooms
for each row
execute function public.set_updated_at();

drop trigger if exists set_devices_updated_at on public.devices;
create trigger set_devices_updated_at
before update on public.devices
for each row
execute function public.set_updated_at();

-- ==========================================
-- เปิด Row Level Security
-- หมายเหตุ: Policy ด้านล่างอนุญาตเฉพาะผู้ใช้ที่ login
-- ==========================================
alter table public.rooms enable row level security;
alter table public.devices enable row level security;
alter table public.device_states enable row level security;
alter table public.sensor_readings enable row level security;

-- อ่านข้อมูลได้สำหรับผู้ที่ Login
create policy "Authenticated users can read rooms"
on public.rooms
for select
to authenticated
using (true);

create policy "Authenticated users can read devices"
on public.devices
for select
to authenticated
using (true);

create policy "Authenticated users can read device states"
on public.device_states
for select
to authenticated
using (true);

create policy "Authenticated users can read sensor readings"
on public.sensor_readings
for select
to authenticated
using (true);

-- Dashboard สามารถอัปเดตสถานะอุปกรณ์ได้หลัง Login
create policy "Authenticated users can insert device states"
on public.device_states
for insert
to authenticated
with check (true);

create policy "Authenticated users can update device states"
on public.device_states
for update
to authenticated
using (true)
with check (true);

-- ==========================================
-- ข้อมูลเริ่มต้น: ห้อง
-- ==========================================
insert into public.rooms (name, description)
values
  ('Living Room', 'ห้องนั่งเล่น'),
  ('Bedroom', 'ห้องนอน'),
  ('Kitchen', 'ห้องครัว'),
  ('Entrance', 'บริเวณหน้าบ้าน')
on conflict (name) do nothing;

-- ==========================================
-- ข้อมูลเริ่มต้น: อุปกรณ์
-- ==========================================
insert into public.devices (
  room_id,
  name,
  device_type,
  icon,
  mqtt_topic
)
values
(
  (select id from public.rooms where name = 'Living Room'),
  'ไฟห้องนั่งเล่น',
  'light',
  '💡',
  'home/living-room/light/set'
),
(
  (select id from public.rooms where name = 'Living Room'),
  'พัดลมห้องนั่งเล่น',
  'fan',
  '🌀',
  'home/living-room/fan/set'
),
(
  (select id from public.rooms where name = 'Entrance'),
  'ประตูหน้าบ้าน',
  'door_lock',
  '🔒',
  'home/entrance/door-lock/set'
),
(
  (select id from public.rooms where name = 'Bedroom'),
  'ไฟห้องนอน',
  'light',
  '🛏️',
  'home/bedroom/light/set'
),
(
  (select id from public.rooms where name = 'Living Room'),
  'Temperature Humidity Sensor',
  'sensor',
  '🌡️',
  'home/living-room/sensor'
)
on conflict (mqtt_topic) do nothing;

-- ==========================================
-- สร้าง initial state ให้กับทุกอุปกรณ์
-- ==========================================
insert into public.device_states (device_id, is_on)
select
  id,
  case
    when name in ('ไฟห้องนั่งเล่น', 'ประตูหน้าบ้าน') then true
    else false
  end
from public.devices
on conflict (device_id) do nothing;

-- ==========================================
-- ข้อมูลเซนเซอร์เริ่มต้น
-- ==========================================
insert into public.sensor_readings (
  device_id,
  temperature,
  humidity,
  light,
  motion
)
values (
  (
    select id
    from public.devices
    where name = 'Temperature Humidity Sensor'
    limit 1
  ),
  28.50,
  63.00,
  420,
  false
);
