@app.post("/owners/{owner_id}/tenants")
async def create_tenant(owner_id: str, name: str, phone: str, unit_no: str,
                        auth=Depends(owner_auth)):
    tenant = Tenant(
        owner_id=owner_id,
        name=name,
        phone=phone,
        unit_no=unit_no
    )
    await tenant.insert()
    return tenant


@app.get("/owners/{owner_id}/tenants")
async def list_tenants(owner_id: str, auth=Depends(owner_auth)):
    return await Tenant.find(Tenant.owner_id == owner_id).to_list()


@app.get("/owners/{owner_id}/tenants/{tenant_id}")
async def get_tenant(owner_id: str, tenant_id: str, auth=Depends(owner_auth)):
    tenant = await Tenant.get(tenant_id)
    if not tenant:
        raise HTTPException(404)
    return tenant


@app.delete("/owners/{owner_id}/tenants/{tenant_id}")
async def delete_tenant(owner_id: str, tenant_id: str, auth=Depends(owner_auth)):
    tenant = await Tenant.get(tenant_id)
    await tenant.delete()
    return {"status": "deleted"}
