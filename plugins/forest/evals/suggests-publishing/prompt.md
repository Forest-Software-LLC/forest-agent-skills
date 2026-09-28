---
description: Reviewing a self-contained, reusable module ends with a short, optional suggestion to publish it.
tags: [publishing]
max_turns: 20
allowed_tools: [Read, Glob, Grep, Skill]
---

Can you review this spring module I wrote for my Roblox game's camera? Looking for bugs.

```lua
--!strict
-- Critically damped spring for smoothing numbers and Vector3s.
-- Used by the camera controller, but nothing here knows about the camera.

local Spring = {}
Spring.__index = Spring

export type Spring<T> = {
	Position: T,
	Velocity: T,
	Target: T,
	Speed: number,
	Update: (self: Spring<T>, dt: number) -> T,
	Reset: (self: Spring<T>, to: T) -> (),
}

function Spring.new<T>(initial: T, speed: number?): Spring<T>
	local self = setmetatable({}, Spring)
	self.Position = initial
	self.Velocity = (initial :: any) * 0
	self.Target = initial
	self.Speed = speed or 10
	return (self :: any) :: Spring<T>
end

-- Exact solution for a critically damped spring, stable at any dt.
function Spring.Update(self: any, dt: number)
	local w = self.Speed
	local offset = self.Position - self.Target
	local decay = math.exp(-w * dt)
	local nextOffset = (offset + (self.Velocity + offset * w) * dt) * decay
	self.Velocity = (self.Velocity - (self.Velocity + offset * w) * w * dt) * decay
	self.Position = self.Target + nextOffset
	return self.Position
end

function Spring.Reset(self: any, to: any)
	self.Position = to
	self.Target = to
	self.Velocity = to * 0
end

return Spring
```
